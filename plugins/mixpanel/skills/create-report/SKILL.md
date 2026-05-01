---
name: create-report
description: Creates Mixpanel reports (Insights, Funnels, Flows, Retention, etc.) from natural language descriptions, handling event selection, filters, breakdowns, and visualization choices. Use when you know what you want to measure but prefer not to build the report manually.
---

# Create Mixpanel Report

Create reports from natural language by discovering events, building report queries, and verifying results.

## Planning First (Critical)

Before any tool calls, decompose the request:

**1. Identify report components:**
- Report type (Insights, Funnels, Flows, Retention, etc.)
- Time range (default: Last 30 Days if not specified)
- Primary event (the action being measured)
- Segment conditions (user filters/breakdowns)
- Filters, breakdowns, cohorts
- Funnel steps (if conversion analysis)

**2. Plan event searches:**
- ONE search per distinct event concept
- Never combine multiple concepts in one search
- Example: "purchase" and "signup" need separate searches

**3. Plan parallel tool calls:**
- Get project info, search events, find cohorts can run together
- Wait for results before building query

## Event Discovery (Critical: Discovery First)

**IMPORTANT: Cast a wide net before narrowing down**

When the user's request is ambiguous or could map to multiple events:
1. Search BROADLY first to discover relevant options
2. Review relevant results - look for related events, custom events
3. Present options to user OR explain your selection rationale
4. Try not to assume a single event is the only answer without exploration

**Search for events:**
```
Get-Events (use include_details=true for full metadata)
```
- Search ONE concept at a time
- Use broad search terms first (e.g., "purchase" not just "purchase completed")
- ✓ Good: "user completes purchase"
- ✗ Bad: "signup or purchase events"

**Make informed decisions, then explain:**
1. Search broadly to discover all options
2. Review results and identify the most comprehensive/accurate approach
3. Make your best judgment call
4. **Always explain**: "I found X, Y, Z options. I chose [X] because [reason]. This includes/excludes [scope]."
5. User can correct if your assumption was wrong

**Verify before use:**
- Check event has actual volume (not zero/stale)
- Get properties with `Get-Properties` and `Get-Property-Values`
- If zero results, search for alternatives

## Report Type Selection

| Report Type | Use When |
|------------|------------|
| `insights` | Counting events/users over time, trends, comparisons, KPIs, distributions, property analytics, segmentation |
| `funnels` | Multi-step conversion analysis with a **known sequence**, drop-off analysis, time between specific events |
| `flows` | Path exploration, discovering **unknown paths** users take, next-step analysis |
| `retention` | User return behavior, cohort retention curves, churn analysis |
| `formulas` | Calculated metrics, ratios, custom KPIs combining multiple events |

### Quick Reference

**insights** - Most versatile. Use for:
- User counts (DAU, WAU, MAU) with unique users
- Event totals, averages, property sums
- Trends, comparisons, distributions
- Segmentation by any property

**funnels** - Conversion analysis. Use for:
- Step-by-step conversion rates when you know the expected sequence
- Time-to-convert between events
- Exclusion events
- Conversion windows

**flows** - Path discovery. Use for:
- Understanding actual paths users take
- Next-step analysis from any event
- Discovering unexpected navigation patterns

**retention** - Return behavior. Use for:
- N-day retention
- Rolling retention
- Bracket retention
- Cohort analysis

**formulas** - Custom calculations. Use for:
- Ratios (conversion rate, revenue per user)
- Combining multiple metrics
- Custom KPIs

## Report Query Structure

**Core parameters (Insights):**
```json
{
  "from_date": "2026-03-01",
  "to_date": "2026-03-31",
  "events": [{
    "event": "Purchase Completed",
    "filters": [],
    "name": "Purchase Completed"
  }],
  "type": "general",
  "unit": "day"
}
```

**Key parameters:**
- `type`: "general" (trends), "unique" (uniques), "average" (averages)
- `unit`: "day", "week", "month", "hour"
- `events`: Array of events to query
- `where`: Global filters (apply to all events)
- `on`: Breakdown property

**Event filters:**
```json
"where": "properties[\"country\"] == \"United States\""
```

**Breakdowns:**
```json
"on": "properties[\"platform\"]"
```

## Workflow: Create Report

1. **Get project context:**
```
Get-Projects (to get project_id)
```

2. **Discover events** (BROAD search first):
```
Get-Events for general event list
Get-Events with event_names=[...] and include_details=true for specific event info
```

3. **Find cohorts if needed:**
```
List cohorts from Lexicon or query
```

4. **Build query** using discovered event names

5. **Build and run query**

**For simple queries** (single event, no breakdowns/filters):
```
Run-Query(
  project_id=X,
  report_type='insights',
  report={
    "name": "DAU Last 7 Days",
    "metrics": [
      {
        "eventName": "$ae_session",
        "measurement": {"type": "basic", "math": "unique"}
      }
    ],
    "chartType": "line",
    "unit": "day",
    "dateRange": {"type": "relative", "range": {"unit": "day", "value": 7}}
  }
)
```

**For complex queries** (breakdowns, filters, formulas, multiple metrics):

1. **First, get the full schema:**
   ```
   Get-Query-Schema(report_type='insights')
   ```
   This returns the complete JSON schema showing all available options

2. **Build your query using the schema:**
   - Use `breakdown` for segmentation by property
   - Use `filters` for event-level or global filtering
   - Use `comparison` for time period comparisons
   - Use `formula` for custom calculations
   - Check schema for exact property names and structure

3. **Run the query:**
   ```
   Run-Query(
     project_id=X,
     report_type='insights',
     report={...full query following schema...}
   )
   ```

**Using skip_results:**
- Add `skip_results=true` if you only need the query_id (e.g., for dashboards)
- Omit or set to `false` if you want the actual data results

6. **Verify results** - check data makes sense

7. **Save to board (optional):**
   - If you used skip_results=true, you already have the query_id
   - Can be added to an existing or new board using Create-Dashboard

## Error Handling

**If query fails:**
- Read error message carefully
- Common issues: incorrect event names, invalid filters, wrong parameter types
- Fix query and retry
- Verify events exist via Get-Events first

**If zero results:**
- Check filters aren't too restrictive
- Verify event has data
- Try broader time range
- Check date format

## Best Practices

**Naming:**
- Include metric + time context: "Weekly Active Users Last 90 Days"
- Not: "WAU" or "Users"

**Time ranges:**
- Default to "Last 30 Days"
- Use specific dates for historical periods
- State interpreted range explicitly

**Verification:**
- Always verify event exists before using
- Check properties with `Get-Properties`
- Confirm values with Get-Property-Values

**Comparisons:**
- Use breakdowns for comparing user segments
- Use date comparisons for period-over-period

**Always include:**
- What the report shows
- Key insights from initial data
- Methodology used
