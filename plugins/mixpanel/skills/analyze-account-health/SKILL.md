---
name: analyze-account-health
description: Summarizes B2B account health by analyzing usage patterns, engagement trends, risk signals, and expansion opportunities using Mixpanel Group Analytics. Use for customer success reviews, renewal preparation, QBRs, or account prioritization.
---

# Analyze Account Health

Deep-dive into a B2B account's product usage to prepare for QBRs, assess renewal risk, identify expansion opportunities, or prioritize CS outreach. Uses Mixpanel Group Analytics to analyze account-level behavior.

## Prerequisites

This skill requires **Group Analytics** to be configured in the Mixpanel project. The account must be identified by a group key (e.g., `company_id`, `org_id`, `account_id`). If Group Analytics is not set up, the skill will note this and suggest configuration steps.

## Instructions

### Step 0: Identify Account & Discover Context

**Get the account identifier:**
- Company name, org ID, account ID, or group property value
- Ask user if not provided

**Discover the data model:**
1. Call `Get-Projects` to confirm the active project.
2. Call `Get-Events` to list available events.
3. Call `Get-Properties` to find group keys (look for properties that represent accounts/orgs/companies). Note the exact group key name.
4. Call `Get-Property-Values` on the group key to verify the account exists in the data.
5. Call `Search-Entities` with the account name to find existing reports, boards, or saved queries for this account. If found, ask user if they want fresh analysis or to review existing.

---

### Step 1: Quick Health Triage

Call `Get-Query-Schema` to understand query structure. Then use `Run-Query` to run these queries in parallel:

**Usage Trend:**
- Query active users (or a core engagement event) filtered to the account's group key value
- Time: Last 60 days, daily interval
- **Shows:** Is activity increasing or decreasing?

**Engagement Quality:**
- Calculate DAU and MAU for the account
- Compute DAU/MAU ratio (stickiness)
- **Shows:** How engaged are active users?

**User Momentum:**
- Active user count week-over-week for the last 8 weeks
- **Shows:** Is the team growing or shrinking?

**Classify Health:**
- **Healthy**: Growing MAU, DAU/MAU >40%, positive WoW trend
- **At-Risk**: Flat/declining MAU, DAU/MAU 20-40%, negative WoW trend
- **Critical**: Steep decline, DAU/MAU <20%, sustained negative WoW trend

---

### Step 2: User-Level Analysis

Use `Run-Query` with user-level breakdowns, filtered to the account:

**Power Users:**
- Top 3-5 users by event volume — these are champions to leverage
- Note their most-used features

**Churned Users:**
- Users active in the previous 30-day period but not in the current 30 days
- These are retention risks

**License Utilization:**
- Active users in last 30 days vs total seats (if seat count is known or ask the user)
- Low utilization = risk signal; high utilization = expansion signal

---

### Step 3: Feature Usage Analysis

Use `Run-Query` grouped by events or features, filtered to the account:

**Feature Breadth:**
- Query the top events by volume for this account
- Ask the user for 5-10 key features if not obvious from event names
- Compute adoption rate per feature (unique users using it / total active users)

**Feature Trends:**
- Usage over last 90 days per key feature
- Identify growing vs declining features

**Focus based on health classification:**
- **If At-Risk/Critical:** Find abandoned features — used 60-90 days ago but not in last 30 days. These may indicate friction or lost value.
- **If Healthy:** Find untapped features — available but not adopted by this account. These represent expansion and deeper adoption opportunities.

---

### Step 4: Behavioral Deep-Dive

**Session Replays (if available):**
Call `Get-User-Replays-Data` for the account's power users and recently churned users. Look for:
- Friction points in core workflows
- Features attempted but abandoned
- Error states or confusion patterns

**Retention:**
Use `Run-Query` to build a retention curve for this account:
- What percentage of users return in week 2, 4, 8?
- Compare to product-wide retention benchmarks

**Funnel Completion:**
If the user identifies key workflows (e.g., onboarding, core action, upgrade flow):
- Query funnel completion rates for this account vs product-wide
- Identify where account users drop off relative to the average

---

### Step 5: Present Account Health Report

Structure output as follows:

```
# Account Health Report: [Account Name]

## Executive Summary
[2-3 sentences: Health classification, key trend, primary recommendation]

## Health Score: [Healthy / At-Risk / Critical]
[One sentence rationale with the key metric that drives the classification]
```

---

**Key Metrics:**

| Metric | Current | Trend | Status |
|--------|---------|-------|--------|
| MAU | X | +/-Y% MoM | Healthy/At-Risk/Critical |
| DAU/MAU (Stickiness) | X% | +/-Y% | Healthy/At-Risk/Critical |
| License Utilization | X/Y (Z%) | +/- | Healthy/At-Risk/Critical |
| Features Adopted | X/Y | +/- | Healthy/At-Risk/Critical |
| Week 4 Retention | X% | vs Z% product avg | Healthy/At-Risk/Critical |

---

**Risk Factors (if any):**

1. **[Issue]** — [Impact]
   - Usage data: [metric/trend]
   - Supporting evidence: [behavioral pattern from replays or funnel data]

**Positive Signals:**

1. **[What's working]** — [Evidence from usage data]

---

**User Intelligence:**

*Champions (Leverage):*
- **[User ID/Name]**: [Activity summary] — *Action: [Specific recommendation]*

*At Risk (Engage):*
- **[User ID/Name]**: [Last active date / declining pattern] — *Action: [Check-in recommendation]*

*Inactive (>30 days):*
- [Count] users ([X]% of licenses)

---

**Feature Adoption:**

| Feature | Users | Adoption % | Trend | Note |
|---------|-------|------------|-------|------|
| [Feature] | X | Y% | Growing | Core strength |
| [Feature] | X | Y% | Declining | Investigate |
| [Feature] | 0 | 0% | — | Expansion opportunity |

*Abandoned features:* [Features used 60-90d ago but not in last 30d]
*Untapped features:* [Available features not yet adopted — expansion opportunity]

---

**Recommendations:**

*This Week:*
1. [Specific action with user/contact name if available]

*This Month:*
1. [Strategic action with context]

*Expansion Opportunities:*
1. [Upsell signal with evidence from usage data]

---

**Analysis Details:**
- Analysis Date: [Date]
- Timeframe: Last [X] days
- Group Key: [property name] = [value]
- Confidence: [High/Medium/Low based on data volume]

---

## Best Practices

- **Always name users** — CS needs who to contact, not aggregates
- **Show trends, not snapshots** — Direction matters more than point-in-time
- **Be specific in recommendations** — "Reach out to user X about feature Y" not "improve engagement"
- **Connect behavior to business** — Declining usage of a premium feature is a churn risk, not just a metric
- **Flag data gaps** — Note low volume, missing group properties, or incomplete data
- **Prioritize by impact** — Focus on issues affecting champions or blocking core workflows

## Common Patterns

**Churn Risks:**
- Champion goes inactive + declining overall usage
- License utilization dropping + core feature usage declining
- Users attempting workflows but dropping off at the same step (funnel friction)

**Expansion Signals:**
- Hitting plan limits (users, events, API calls)
- High adoption of advanced features + growing user count
- New users being added consistently + strong retention

**Health Recovery:**
- Recently churned users returning after feature update
- Adoption of a new feature correlating with improved retention
- Power user count growing

## Edge Cases

- **No Group Analytics.** If no group key exists, report this: "This project doesn't have Group Analytics configured. To use account-level analysis, set up a group key (e.g., `company_id`) in your Mixpanel project settings. See docs.mixpanel.com/docs/data-structure/group-analytics." Fall back to user-level analysis if the user provides specific user IDs.
- **Very small account.** If <5 active users, skip statistical analysis and provide a per-user narrative instead. Every user matters individually at this scale.
- **No Session Replay data.** Skip the behavioral deep-dive and note that enabling Session Replay would provide richer qualitative context.
- **Account not found.** If the group key value doesn't match any data, check for typos, alternate naming, and suggest using `Get-Property-Values` to list available accounts.
- **Multiple group keys.** Some projects have hierarchical groups (workspace → team → user). Ask the user which level to analyze.

## Examples

### Example 1: QBR Preparation

User says: "Prepare account health for Acme Corp"

Actions:
1. Discover group key and confirm Acme Corp exists in the data
2. Run health triage — MAU trend, stickiness, user momentum
3. Identify power users and churned users
4. Analyze feature adoption breadth and trends
5. Pull session replays for top users
6. Present full health report with recommendations

### Example 2: Renewal Risk Assessment

User says: "Is CustomerX at risk of churning?"

Actions:
1. Find CustomerX in group data
2. Focus on declining signals — MAU trend, churned users, abandoned features
3. Compare current engagement to 90-day-ago baseline
4. Identify specific friction points from funnel analysis
5. Present risk assessment with concrete save actions

### Example 3: Expansion Opportunity

User says: "Which accounts are ready for upsell?"

Actions:
1. Query across multiple accounts — high utilization, growing user counts, premium feature adoption
2. Rank accounts by expansion readiness
3. For the top 3-5, provide specific evidence (hitting limits, feature requests, usage patterns)
4. Present with recommended upsell approach per account
