---
name: analyze-board
description: Deeply analyze Mixpanel boards by analyzing key reports, surfacing top areas for concern and takeaways, identify anomalies, then explain changes using context
---

# Analyze Board

## When to Use

- Meeting prep: Synthesize a board into talking points before a review or exec meeting
- Cross-report pattern detection: Spot correlations across multiple reports that are hard to see manually
- Board investigation: A key number moved in a report within this board and you want to explain why
- Onboarding to unfamiliar boards: Get up to speed on what a board tracks and its current state

## Instructions

### Step 1: Retrieve the Board

Use `Mixpanel:Get-Dashboard` with the dashboard ID to get the full structure and report IDs.

### Step 2: Query Key Reports

Query up to 3-5 reports at a time. Prioritize:

1. Primary KPI reports (usually at the top)
2. Reports with recent changes
3. Trend-based visualizations

Use `Mixpanel:Run-Query` for each report.

### Step 3: Analyze Patterns

When analyzing reports, focus on the most decision-relevant signals:
  - KPI tiles: Context (timeframe, user type) and % change if shown
  - Line / Time series: Trends, slope changes, or notable events (not right-edge noise)
  - Funnel: Major drop-off steps or unexpected patterns
  - Bar / Categorical: Concentrations, gaps, or surprising distributions
  - Retention: Compare segments at key intervals (Day 1, Day 7, Day 30)
  - Tables: Top contributors, dominant players, distribution imbalances

### Step 4: Synthesize Findings

Present a structured summary:

1. **Overall Health**: Concise one-liner of THE top takeaway or key takeaways
2. **Areas of Concern 🚩**: Top 1-3 urgent issues or negative metric trends
3. **Key Takeaways 💡**: Top 1-3 most important or surprising insights
4. **Recommendations**: Top 3 specific actionable recommendations with priority levels [p0],[p1],[p2]

## Best Practices

- Be comprehensive in analysis but concise in response
- Do not repeat the same takeaway across sections
- Always link referenced reports using markdown
- Flag metrics that changed more than 10% week-over-week
- Note any data quality issues
- Always attribute findings to specific reports
- Each recommendation should be 1 concise actionable bullet
- Do not recap process at the end
