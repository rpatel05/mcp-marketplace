---
name: replay-ux-audit
description: Watches multiple session replays for a specific flow and synthesizes a ranked friction map - common pain points, error patterns, and UX improvements
---

# replay-ux-audit

Audit a user flow by analyzing multiple session replays to find UX friction.

## When to Use

- Investigating conversion drop-offs
- New feature UX review
- Pre-launch usability check
- Understanding user confusion

## Input

**Flow to audit:** (e.g., "Checkout flow")
**Number of replays:** 10-20 recommended
**Filter criteria:** Cohort or user segment

## Analysis Steps

### Step 1: Sample Replays

Get 10-20 diverse replays:
- Mix of successful and failed attempts
- Different platforms if relevant
- Recent sessions (past 7 days)

### Step 2: Watch and Tag

For each replay, tag:
- **Confusion**: User hesitates, backtracks
- **Errors**: Form errors, failed actions
- **Friction**: Slow steps, repeated attempts
- **Success**: Smooth completion

### Step 3: Synthesize Patterns

Identify:
- Most common friction points
- Error frequency and types
- Time spent per step
- Abandonment triggers

## Output Format

**UX Audit: [Flow Name]**

**Sample:** [N] replays analyzed

**Friction Map (Ranked by Frequency)**

1. **[Step/Element]**
   - Issue: [Description]
   - Frequency: [X/N replays]
   - Impact: [High/Medium/Low]
   - Example: [Session ID]
   - Fix: [Specific recommendation]

2. **[Step/Element]**
   [Same structure]

**Error Patterns**
- [Error type]: Seen in [X%] of replays
- [Error type]: Seen in [X%] of replays

**Time Analysis**
- Average time to complete: [X seconds]
- Longest step: [Step name] ([X seconds])

**Quick Wins**
1. [High impact, low effort fix]
2. [High impact, low effort fix]

**Prioritized Recommendations**
- [p0] [Critical fix with evidence]
- [p1] [Important improvement]
- [p2] [Nice-to-have enhancement]

## Best Practices

- Watch both successful and failed sessions
- Note exact timestamps for friction points
- Link to specific replay examples
- Quantify frequency (not just anecdotes)
- Make recommendations specific and actionable
