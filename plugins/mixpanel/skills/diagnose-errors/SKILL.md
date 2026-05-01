---
name: diagnose-errors
description: >
  Investigates errors across custom error events, failed API calls, and exception tracking to identify what's broken, where, and why. Use when the user says "what's broken", "errors are up", "why are users seeing errors", "JS errors", "API failures", "something is broken", or wants to triage product reliability issues.
---

# Error Diagnosis & Triage

Investigate product errors by discovering and triaging error-related events in the user's Mixpanel project — failed API calls, JavaScript exceptions, UI error states, and other custom error tracking — to identify what's broken, which users are affected, and what's causing it. This skill cross-references multiple error signals to surface causal chains rather than treating each in isolation.

This is a **reactive investigation** skill — the user has a signal (spike, complaint, experiment regression, gut feeling) and wants to understand what's happening. For proactive monitoring, use the `monitor-reliability` skill instead.

---

## CRITICAL: Discovering Error Events

Unlike platforms with auto-captured error events, Mixpanel projects track errors using custom events. The first step is always to **discover what error events exist** in the project. Common patterns include:

- Events named with "error", "exception", "fail", "crash", "bug" in the name
- Events like `API Error`, `JS Exception`, `Error Displayed`, `Request Failed`, `App Crash`
- Properties like `error_message`, `error_code`, `status_code`, `error_type`, `stack_trace`, `endpoint`, `page`, `url`

Never assume event or property names — always use `Get-Events` and `Get-Properties` to discover the actual schema.

---

## Instructions

### Step 1: Context & Scope

1. Call `Get-Projects` to identify the active project. If multiple projects, ask which to investigate.
2. Call `Get-Events` to list all events. Scan for error-related events — look for names containing "error", "fail", "exception", "crash", or any event the user references.
3. For each discovered error event, call `Get-Properties` to understand its schema. Note the property names exactly as they appear.
4. Determine the investigation scope from the user's request:
   - **Broad triage**: "What's broken?" → scan all error events for the biggest problems
   - **Targeted**: "API errors are up" → start with the API error event, then check for cascading errors
   - **Specific error**: "Users are seeing TypeError" → filter to that error message
5. Determine the time window. Default to the last 7 days with daily granularity unless the user specifies otherwise.

### Step 2: Quantify the Error Landscape

Call `Get-Query-Schema` to understand the available query structure. Then use `Run-Query` to build queries. Run these in parallel where possible. Budget: 4-6 calls for this step.

#### 2a. Error Volume & Trends

For each discovered error event:

1. **Error volume trend.** Query daily event counts and unique users over the time window. Flag day-over-day spikes >25%.
2. **Top errors.** Group by the error message/type property to find the highest-volume errors. Include error code or type for context.
3. **New vs. chronic.** Compare errors in the recent window (last 7 days) to the prior period (7 days before that). Errors appearing only in the recent window are likely regressions. Errors present in both are chronic.

#### 2b. API / Network Failures (if tracked)

If the project has API or network request events:

1. **Failure rate trend.** Filter to error status codes (4xx, 5xx) or failure indicators. Measure daily counts and unique users. Compare to total request volume for a failure rate percentage.
2. **Top failing endpoints.** Group by URL or endpoint property. Include status code as a secondary grouping to distinguish auth errors (401) from server errors (500) from not-found (404).
3. **Slow endpoints (if duration is tracked).** Flag endpoints with high latency.

#### 2c. UI Error States (if tracked)

If the project tracks error display events, error clicks, or error dismissals:

1. **Volume trend.** Daily error display count. Spikes indicate users are actively encountering error states.
2. **What users are seeing.** Group by error message or UI element to see which error states are most common.

### Step 3: Cross-Event Correlation

This is where the skill adds value beyond looking at each event in isolation.

1. **Failed request → exception chain.** Compare the timing and pages of API failures (Step 2b) with JS errors (Step 2a). If the same pages have both failed requests AND exceptions, the API failure is likely the root cause. Use page or screen properties as the join dimension.

2. **Error → frustration chain.** Compare error events with UI error states or rage clicks if tracked. High error display volume on pages with high exception rates confirms users are seeing the broken experience.

3. **Page-level triage.** Use `Run-Query` to group error events by page or screen property. Produce a page-level error heatmap:
   - Pages with API failures + exceptions + error displays = **critical** (full causal chain)
   - Pages with exceptions + error displays but no API failures = **frontend bug**
   - Pages with API failures but no exceptions = **backend issue, gracefully handled**
   - Pages with exceptions but no error displays = **silent errors** (may not affect UX)

### Step 4: Identify Affected Users & Segments

For the top 2-3 error patterns from Step 3:

1. **User scope.** Use `Run-Query` to count unique users affected. Compare to total active users for an impact percentage.
2. **Segment breakdown.** Group by available user properties (platform, browser, country, plan tier, os) to determine if errors concentrate in a specific segment. Use `Get-Properties` if you need to discover available user properties.
3. **Session Replays.** Call `Get-User-Replays-Data` for users who experienced the error. Provide 2-3 replay links so the user can watch exactly what happened.

### Step 5: Root Cause Hypothesis

Build a root cause hypothesis using evidence from the prior steps:

1. **Temporal pattern.** Is the error constant, intermittent, or growing? Constant suggests a code bug. Intermittent suggests infrastructure. Growing suggests a progressive failure (memory leak, queue backlog).
2. **Release correlation.** If error spikes align with a known deployment date (ask the user or check for deploy-tracking events), it's the leading hypothesis.
3. **Segment concentration.** If errors only affect one browser, platform, or plan tier, the root cause likely involves that specific environment or feature gate.
4. **Experiment correlation.** If the user mentions an experiment or if errors concentrate in a segment that maps to an experiment variant, use `Run-Query` to check error rates by variant.

### Step 6: Present the Diagnosis

Structure the output as a triage report. Lead with what's most broken and actionable.

**Required sections:**

1. **Diagnosis summary** (2-3 sentences): The single most important finding. Written as a headline you'd send to the engineering lead. Include scope: how many users, which pages, since when.

2. **Error landscape** — A table summarizing the state across discovered error signals:

```
| Signal | Volume (7d) | Trend | Top Source | Severity |
|--------|-------------|-------|------------|----------|
| [Error Event 1] | [N] events, [N] users | [+/-]% WoW | [top error message] | [Critical/High/Medium/Low] |
| [Error Event 2] | [N] events, [N] users | [+/-]% WoW | [top error message] | ... |
```

3. **Top errors** (3-5 max): Each as a narrative paragraph:
   - **[Error headline]** — What's happening (the error), where (page/endpoint), who's affected (user count/segment), since when, and what to do (specific fix action). Include replay links inline.

4. **Causal chains** (if found): Describe the cross-event chain. Example: "POST to `/api/query` is returning 500 → this triggers an unhandled TypeError on the dashboard page → users see the error modal and retry. ~1,200 users affected in the last 7 days."

5. **Recommended actions** (2-4 numbered items): Concrete and specific. Start each with a verb. Bias toward fixing, investigating further with a specific breakdown, or setting up monitoring.

6. **Follow-on prompt**: Ask what to dig into next — e.g., "Want me to segment the API failures by plan tier, watch a few session replays, or build a monitoring board for these errors?"

**Severity classification:**

| Severity | Criteria |
|----------|----------|
| **Critical** | >5% of users affected, full causal chain, or blocking a core flow |
| **High** | 1-5% of users, errors on key pages, or a clear regression |
| **Medium** | <1% of users, chronic errors, or errors on non-critical pages |
| **Low** | Silent errors with no user-facing impact, or isolated to an edge-case segment |

---

## Edge Cases

- **No error events found.** The project may not track errors as discrete events. Report this clearly: "This project doesn't appear to have dedicated error-tracking events. Consider instrumenting error events (e.g., `Error Displayed`, `API Error`, `JS Exception`) to enable error diagnosis." Suggest common error tracking patterns.
- **Very high error volume.** If >100K errors in the window, focus on unique error messages and affected user counts, not raw event counts. Group aggressively.
- **All errors are chronic.** If nothing is new, frame findings as tech debt priorities rather than regressions.
- **Error data is sparse.** If only one error event type exists, work with what's available. Note which signals are missing and what they would add.
- **User asks about a specific error message.** Skip the broad landscape scan (Step 2) and go directly to filtering by that error message. Then check for correlated events.
- **User asks about a specific user or account.** Scope all queries to that user/account. Provide a session-level view using `Get-User-Replays-Data`. Prioritize replay links.

## Examples

### Example 1: Broad Error Triage

User says: "What's broken right now?"

Actions:
1. Get project context and discover error events
2. Query all error events for the last 7 days — volume, trend, top sources
3. Cross-reference by page to find causal chains
4. Surface the 3-5 biggest issues ranked by user impact
5. Provide replay links for the worst pattern

### Example 2: Regression Investigation

User says: "Errors seem up since yesterday's deploy"

Actions:
1. Get project context and error events
2. Query error events comparing pre-deploy (7d before) vs post-deploy (last 24h)
3. Identify new error messages that didn't exist before
4. Check if new errors correlate with API failures
5. Segment by page and feature to isolate the blast radius
6. Present findings anchored to the deployment date

### Example 3: Specific Error Deep-Dive

User says: "We're seeing a lot of TypeErrors in the chart builder"

Actions:
1. Filter error events to TypeError and chart builder pages
2. Group by error message and source file to find specific errors
3. Check API/network events on the same pages for failing calls
4. Pull session replays of users who hit the TypeError
5. Present the error with reproduction steps derived from replays
