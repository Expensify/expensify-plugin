# Expensify Plugin for Claude

Connect Claude to your Expensify account and analyze your financial data using natural language.

## What this plugin does

This plugin connects Claude to the Expensify MCP server, giving Claude live access to your expenses, reports, receipts, trips, and workspace data. It includes two built-in skills for common financial workflows.

## Skills

### `/expensify:spend-brief`

Generate a plain-English spend summary for any time period. Claude pulls your Expensify transactions, organizes them by total, category, person, and vendor, flags anything unusual, and writes a narrative with recommended next steps.

**Example:** "Give me a spend brief for last month" or `/expensify:spend-brief Q3 2026`

### `/expensify:flux-analysis`

Compare spending across two periods to identify what changed and why. Claude surfaces the biggest movers by category and vendor, and explains the variance in plain English.

**Example:** "What changed in our spending last month vs. the month before?" or `/expensify:flux-analysis`

## Setup

1. Install this plugin from the Claude plugin directory.
2. When prompted, sign in to your Expensify account to authorize Claude's access.
3. Claude can now answer questions about your expenses and use the skills above.

## Privacy

Claude accesses only the Expensify account you authorize. Your data is scoped to your account and workspace(s) and is not shared with third parties. See [Expensify's privacy policy](https://use.expensify.com/privacy).
