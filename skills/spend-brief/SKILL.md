# Spend Brief

Triggered by `/expensify:spend-brief`. Produces a plain-English spend summary for a given time period, organized by total, category, person, and vendor, with anomaly flags and recommended next steps.

## When to use

Use this skill when the user asks for a spending summary, overview, or brief for a period — for example "give me a spend brief for last month" or "summarize our Q3 spending."

## Steps

1. **Determine the period.** If the user specified a period, use it. If not, default to the current calendar month and confirm with the user before proceeding.

2. **Get the authenticated user.** Call `GetCurrentUser` to get the user's email and accountID.

3. **Get workspaces.** Call `GetWorkspaceList` to find the user's workspaces. If there is more than one, ask the user which workspace to analyze, or analyze all of them if they say so. Note the policyID(s) for use in subsequent searches.

4. **Run four parallel searches** for the period using `Search` with `type: "expense"`, `shouldCalculateTotals: true`, and the appropriate date filters on `"date"`. Run each with a different `groupBy`:
   - `groupBy: "category"` — spending by category
   - `groupBy: "from"` — spending by person
   - `groupBy: "merchant"` — spending by vendor (top vendors)
   - No `groupBy`, with `status: "all"` — full transaction list for anomaly detection (limit to top expenses by amount if the result set is large)

   Include the `policyID` in each search to scope to the workspace. For personal spend questions, omit `policyID`.

5. **Calculate totals.** Use the `search.total` and `search.count` from the `shouldCalculateTotals` response as the headline figures.

6. **Flag anomalies.** Look for:
   - Any single expense that is unusually large relative to the category average
   - Categories with spending significantly above or below the prior comparable period (if data is available)
   - Vendors that appear only once and carry a high amount
   - Missing receipts on expenses above a reasonable threshold

7. **Write the brief.** Structure it as:

   **[Period] Spend Brief**

   **Headline:** Total spend, transaction count, number of people with expenses.

   **By category:** Top 5–7 categories by spend, each with total and % of overall.

   **By person:** Top spenders with their totals (omit if this is a personal account with one user).

   **By vendor:** Top 5–7 vendors by total spend.

   **Anomalies:** Bulleted list of anything that stands out. If nothing stands out, say so briefly.

   **Next steps:** 2–3 concrete, actionable recommendations based on what the data shows — for example, following up on missing receipts, reviewing a category that spiked, or submitting outstanding reports.

   End the brief with a link to the full search in Expensify using `search.searchURL` from one of the Search responses, so the user can drill in.

## Notes

- Amounts in Search results are in cents. Divide by 100 and format as currency when presenting to the user.
- Use the `personalDetailsList` map to resolve accountIDs to names/emails when presenting per-person breakdowns.
- If the workspace has no expenses in the period, say so clearly and suggest checking a broader date range.
