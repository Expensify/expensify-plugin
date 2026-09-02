# Flux Analysis

Triggered by `/expensify:flux-analysis`. Compares spending across two periods — current vs. prior — identifies significant changes by category and vendor, and explains what drove the variance.

## When to use

Use this skill when the user asks about spending changes, variances, or trends — for example "what changed in our spending last month vs. the month before?" or "run a flux analysis on Q3 vs Q2."

## Steps

1. **Determine the periods.** Identify the current period and the prior comparison period from the user's request. If not specified, default to current month vs. prior month. Confirm before proceeding.

2. **Get workspaces.** Call `GetWorkspaceList` to find the user's workspaces and note the relevant policyID(s).

3. **Search with flux filters.** Call `Search` with `type: "expense"`, `shouldCalculateTotals: true`, and a `fluxPeriod` filter. The `fluxPeriod` filter compares the current period against an equivalent prior period automatically. Use `sortBy: "flux"` to surface the largest movers first. Run two searches:
   - `groupBy: "category"` — variance by category
   - `groupBy: "merchant"` — variance by vendor

   Include `policyID` to scope to the workspace.

4. **Identify significant movers.** From the results, surface:
   - Categories or vendors where spending increased or decreased by more than 20% (or a meaningful absolute amount)
   - Any category or vendor that appeared in one period but not the other
   - The single largest absolute dollar variance

5. **Write the analysis.** Structure it as:

   **Flux Analysis: [Current Period] vs. [Prior Period]**

   **Headline:** Total spend in each period, absolute change, and % change.

   **Biggest increases:** Top 3–5 categories or vendors that grew the most, with prior amount, current amount, and % change.

   **Biggest decreases:** Top 3–5 categories or vendors that shrank the most.

   **New this period:** Any categories or vendors that had zero spend in the prior period.

   **Dropped off:** Any categories or vendors present in the prior period but absent this period.

   **What this means:** A 2–3 sentence plain-English interpretation of what drove the overall change — for example, "Most of the increase came from travel and software, while meals declined. The software spike is concentrated in two vendors..."

   **Next steps:** 1–2 concrete actions, such as reviewing a specific category with a reviewer or checking whether a budget threshold was exceeded.

   End with a link to the Expensify search using `search.searchURL` so the user can explore the details.

## Notes

- Amounts are in cents. Divide by 100 and format as currency.
- Time-period `groupBy` values (month, week, year, quarter) are incompatible with `fluxPeriod` — do not combine them. Use `groupBy: "category"` or `groupBy: "merchant"` with `fluxPeriod`.
- `fluxBasis` in the response is the prior-period total; `fluxAmount` is the change. Both are in cents.
- If the dataset is large, focus on the top 10 movers in each direction rather than listing everything.
