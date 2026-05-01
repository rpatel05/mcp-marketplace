---
name: monitor-reliability
description: >
  Delivers a reliability health check using error events, failed requests, and exception data in Mixpanel. Use when the user asks for a "reliability check", "error rate", "quality metrics", "page health", "did the release break anything", "error budget", or wants a proactive product quality report.
---

# Reliability Monitor

Proactive reliability advisor that delivers a structured quality health check from error and performance events in Mixpanel. The goal is to surface whether the product is healthy, degrading, or broken — and where — so the user knows what needs attention before users complain.

This is a **proactive monitoring** skill. The user may not know anything is wrong — your job is to tell them. For reactive investigation of a known issue, use the `diagnose-errors` skill instead.

---

## CRITICAL: Discovering Error & Performance Events

Mixpanel projects track errors and performance using custom events. Before computing any KPIs, you must discover what's available. Common patterns:

**Error events:** Names containing "error", "exception", "fail", "crash"
- e.g., `API Error`, `JS Exception`, `Error Displayed`, `Request Failed`

**Performance events:** Names containing "request", "load", "latency", "performance"
- e.g., `API Request`, `Page Load`, `App Launch`

**Properties to look for:** `error_message`, `error_code`, `status_code`, `duration`, `endpoint`, `page`, `url`, `success` (boolean)

Never assume event or property names — always use `Get-Events` and `Get-Properties` to discover the actual schema.

---

## CRITICAL: Managing Response Sizes

1. **`Run-Query` results can be large.** When grouping by endpoint or error message, limit to 10-20 top values to avoid pulling the entire long tail.
2. **Parallelize where possible.** Queries for different events can run in parallel.
3. **One time window, two purposes.** Query the full 14-day window. Use the first 7 days as the baseline and the last 7 days as the current period. This avoids making separate calls for each period.

---

## Report Structure

The report has three parts:

1. **Health Summary** (top) — KPI table + overall verdict. Someone reading only this section knows if they need to worry.
2. **Page Health** (middle) — Per-page reliability scores. Identifies which product areas are worst.
3. **Details & Actions** (bottom) — What changed, what's new, what to do about it.

If the user provides a deployment date or says "did the release break anything," add a **Release Comparison** section between Health Summary and Page Health.

---

## Instructions

### Phase 1: Context & Baseline

1. Call `Get-Projects` to identify the active project.
2. Call `Get-Events` to list all events. Identify error events, performance events, and any request-tracking events.
3. For each relevant event, call `Get-Properties` to understand its schema — note the exact property names for error messages, status codes, durations, pages, etc.
4. Call `Get-Query-Schema` to understand available query operators and structures.
5. Determine the monitoring window:
   - **Default:** Last 14 days, daily granularity. Days 1-7 = baseline, days 8-14 = current.
   - **Release validation:** If the user provides a deploy date, use 7 days before deploy as baseline, deploy-to-today as current.

### Phase 2: Compute Reliability KPIs

Run these in parallel where the project has the relevant events. Budget: 4-6 calls for this phase.

#### 2a. Request / API Reliability

If the project tracks API or network request events:

1. **Failure rate.** Count events with error status codes (4xx, 5xx) or `success = false`, divided by total request events, per day. Compute current-period average and baseline average. Flag if current > baseline by >20% relative.
2. **Slow request rate.** If duration is tracked, count events where duration exceeds 3000ms as a percentage of total requests per day.
3. **Top failing endpoints (current period only).** Group by endpoint/URL property, filter to failures, limit to top 10. Include status code distribution.

#### 2b. Error / Exception Health

If the project tracks error or exception events:

1. **Error rate.** Daily error count and unique users affected. Compute current vs baseline averages.
2. **Error-free user rate.** Count users with zero error events as a percentage of total active users. This is the headline quality KPI.
3. **New errors.** Group by error message in both periods. Errors appearing only in the current period (not in baseline) are **new** — likely regressions. Flag these prominently.
4. **Top errors (current period).** Group by error message, limit to top 10. Include error type, source, and unique user count.

#### 2c. User Frustration

If the project tracks UI error states, error clicks, rage clicks, or error dismissals:

1. **Error display rate.** Daily volume and unique users encountering error states. Compute current vs baseline.
2. **Top error states.** Group by error message or UI element, limit to top 5.

If no frustration events exist, note this gap in the report.

### Phase 3: Page Health Scoring

Use `Run-Query` to score individual pages/screens. Budget: 1-2 calls.

1. Query error events grouped by page or screen property for the current period. For each page, compute:
   - Error/exception count
   - Failed request count (if tracked)
   - Error display count (if tracked)
   - Unique users affected

2. **Score each page:**

| Grade | Criteria |
|-------|----------|
| **Healthy** | All signals below product-wide average |
| **Degraded** | 1-2 signals above average, or any signal >2x average |
| **Unhealthy** | All signals above average, or any signal >5x average |
| **Critical** | Any signal >10x average, or >5% of page visitors affected |

3. Rank pages by severity. Surface the worst 5-10 pages.

### Phase 4: Release Comparison (only if deploy date provided)

If the user asked about a specific release:

1. **Before vs after.** Compare KPIs from Phase 2 using pre-deploy and post-deploy windows.
2. **New errors post-deploy.** Errors appearing only after the deploy date are regression candidates. List with error message, source, and affected user count.
3. **Endpoints affected.** Compare failure rates by endpoint pre/post deploy.
4. **Verdict:**
   - **Clean** — No significant changes in any reliability KPI
   - **Minor regressions** — 1-2 new errors or small failure rate increases, <1% of users affected
   - **Significant regressions** — New errors affecting >1% of users, or failure rate increase >50% relative
   - **Rollback candidate** — Critical new errors, or failure rate increase >100% relative affecting core flows

### Phase 5: Validate

Be the skeptic before presenting:

1. **Partial-day artifacts.** If today is included, compare pace (per-hour rate) not raw totals.
2. **Day-of-week effects.** Compare same days across weeks. Weekend vs weekday traffic differences can create false signals.
3. **Bot traffic.** Very high request volumes with 4xx errors on API endpoints may be bots or scrapers. Note if the pattern looks non-human.
4. **Expected errors.** 401s on auth endpoints during login flows are normal. 404s on user-generated content URLs are expected. Don't flag these unless they spike.

### Phase 6: Build the Report

**Required sections:**

#### 1. Health Summary

```
## Reliability Report: [Project Name]
Date: [Today] | Window: [Start] - [End] | Project: [Name] ([ID])

| KPI | Current (7d) | Baseline (7d) | Change | Status |
|-----|-------------|---------------|--------|--------|
| Request failure rate | X.X% | X.X% | +X.X% | [Healthy/Degraded/Critical] |
| Slow request rate (>3s) | X.X% | X.X% | +X.X% | ... |
| Error rate (errors/DAU) | X.XX | X.XX | +X.XX | ... |
| Error-free user rate | XX.X% | XX.X% | -X.X% | ... |
| Users affected by errors | X,XXX | X,XXX | +XX% | ... |

**Overall: [Healthy / Degraded / Unhealthy / Critical]**
[1-2 sentence summary of the overall state.]
```

Status thresholds:
- **Healthy**: Stable or improving (change <10% relative)
- **Degraded**: Change 10-50% relative
- **Critical**: Change >50% relative, or absolute value exceeds acceptable threshold

Only include KPI rows for which the project has data. Note any missing signals.

#### 2. Release Comparison (conditional)

Only if the user asked about a release or a deploy clearly correlates. Use the format from Phase 4 with the verdict.

#### 3. Page Health

```
### Page Health

| Page | Errors | Failed Requests | Error Displays | Users Affected | Grade |
|------|--------|-----------------|----------------|----------------|-------|
| /path | XXX | XXX | XXX | X,XXX | Critical |
| /path | XXX | XXX | XXX | XXX | Degraded |
```

Only show degraded, unhealthy, or critical pages. If all pages are healthy, say so in one line.

#### 4. What Changed

Narrative paragraphs (3-5 max) covering the most important changes. Each paragraph follows the pattern:

**[Headline]** — What changed (with numbers and time context). Why it likely changed (deployment, experiment, external factor — or "no clear cause yet"). Who's affected (segment, page, user count). What to do about it (specific action).

Prioritize:
1. **New errors** (regressions) over chronic errors (tech debt)
2. **Worsening trends** over stable-but-bad metrics
3. **User-facing impact** over silent errors
4. **Core flow errors** over peripheral page errors

#### 5. Recommended Actions

2-4 numbered items, ordered by urgency. Concrete and specific. Start each with a verb.

For each action, note the expected impact: "Fixing the TypeError in the dashboard component would eliminate ~40% of current errors, improving the error-free user rate by an estimated 2 percentage points."

#### 6. Follow-on Prompt

Ask what to dig into: "Want me to investigate the dashboard errors in detail, build a monitoring board to track these KPIs daily, or compare error rates across plan tiers?"

---

**Writing standards:**

- **Numbers first.** This is an engineering-oriented report. Lead with data, not narrative.
- **Approximate.** "~2.3%" not "2.2847%". "~1,200 users" not "1,237 users."
- **Always state the time anchor.** "Over the last 7 days" or "since the March 15 deploy." Never "recently."
- **Relative and absolute.** Always provide both: "Failure rate increased from 1.2% to 1.8% (+50% relative)."
- **Don't bury the lede.** If a release broke something, say it in the first sentence.
- **Total report length: 500-800 words** for the core report. Tables don't count toward this.

---

## Edge Cases

- **No error events found.** Report this clearly: "This project doesn't have dedicated error-tracking events. To enable reliability monitoring, instrument events like `API Error` (with properties: `endpoint`, `status_code`, `error_message`), `JS Exception` (with `error_type`, `error_message`, `stack_trace`), and `Error Displayed` (with `error_message`, `page`)." Provide a concrete tracking plan.
- **Very low traffic.** If <1,000 events in the window, note that sample sizes are too small for reliable rates. Report absolute counts instead of percentages.
- **All metrics are healthy.** This is a positive finding. Say so clearly. Then surface the top chronic errors as tech debt candidates.
- **No baseline available.** If the project just started tracking, skip trend comparisons and present the current snapshot. Recommend checking back in 2 weeks.
- **User asks "did the release break anything" without a date.** Ask the user for the deploy date or check for deploy-tracking events in the project.
- **Overwhelming number of errors.** If >50K errors in the window, focus on unique error messages and affected user counts. Group by source/file first to identify noisiest areas, then drill into messages.

## Examples

### Example 1: Routine Health Check

User says: "Give me a reliability check"

Actions:
1. Get project context and discover error/performance events
2. Query all relevant events over 14 days — 7-day baseline vs 7-day current
3. Compute all available KPIs and page health scores
4. Present the health summary table, worst pages, and top actions

### Example 2: Release Validation

User says: "We shipped v4.3 yesterday — did it break anything?"

Actions:
1. Get project context and discover error events
2. Query events with pre-deploy (7d before) and post-deploy windows
3. Identify new errors that didn't exist before the deploy
4. Compare failure rates and page health pre/post
5. Deliver a release verdict: clean, minor regressions, significant regressions, or rollback candidate

### Example 3: Targeted Area Check

User says: "How's reliability on the checkout flow?"

Actions:
1. Get project context and discover error events
2. Filter all error events to checkout-related pages
3. Compute KPIs scoped to those pages
4. Compare to product-wide averages
5. Present a focused report with actions specific to checkout
