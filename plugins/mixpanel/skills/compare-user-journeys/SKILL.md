---
name: compare-user-journeys
description: Compare behavioral paths between two user segments (e.g., converters vs. churners, power users vs. casual) to identify what differentiates successful users.
---

# Compare User Journeys

## When to Use

- Understand why some users convert and others don't
- Identify behaviors that predict retention or churn
- Find what makes power users different from average users
- Debug why a specific cohort or segment underperforms

## Instructions

### Step 1: Define Segments to Compare

Ask the user which segments to compare. Common comparisons:
- **Converters vs. non-converters** (free trial → paid)
- **Retained vs. churned** (active after 30 days vs. not)
- **Power users vs. casual users** (top 10% engagement vs. median)
- **High LTV vs. low LTV** (revenue-based segments)
- **Activated vs. not activated** (completed onboarding vs. dropped off)

### Step 2: Define Journey Window

- **Time window**: First 7 days? First 30 days? Last 90 days?
- **Key outcome event**: What defines "success"? (e.g., "Purchase Completed", "Day 7 Active")

### Step 2.5: Define Segment Filters

If comparing segments that don't exist as saved cohorts in Mixpanel, define them using query filters:

**Common segment definitions:**

**Converters vs. Non-converters:**
- Segment A (Converters): Users who did "Purchase Completed" in time window
- Segment B (Non-converters): Users who did NOT do "Purchase Completed"
- Implementation: Use separate queries with event filters

**Power users vs. Casual users:**
- Segment A (Power): Users with high engagement (e.g., 20+ sessions in 30 days)
- Segment B (Casual): Users with low engagement (e.g., <5 sessions in 30 days)
- Implementation: Filter by user profile property or event frequency

**Retained vs. Churned:**
- Segment A (Retained): Users who returned (did return event in retention window)
- Segment B (Churned): Users who didn't return
- Implementation: Use retention query or event occurrence filters

**How to apply segment filters in queries:**

For event-based segments:
```
report={
  "metrics": [...],
  "filters": [
    {
      "property": "event_name",
      "operator": "equals",
      "value": "Purchase Completed"
    }
  ]
}
```

For user profile segments:
```
report={
  "where": "user[\"plan_type\"] == \"paid\""
}
```

For behavioral segments (did/didn't do event):
- Run separate queries for each segment
- One with the event present, one without
- Compare the results side-by-side

**Pro tip:** If these segments will be used frequently, ask the user to create saved cohorts in Mixpanel first, then reference them by cohort ID in queries.

### Step 3: Query Both Segments

For each segment, get:

**Behavioral data:**
- Top 10 most common events
- Event frequency (events per user)
- Event sequences (which events happen before the outcome)
- Feature adoption (which features do they use)

**Timing data:**
- Time to first key action
- Session frequency
- Days active in the window

Use Mixpanel queries:
- `Run-Query` with segment filters
- Flow analysis to see paths
- Funnel analysis for conversion steps

### Step 4: Identify Differentiators

Compare the segments to find:

**Behavioral differences:**
- Events that one group does significantly more (2x+ difference)
- Events unique to one group
- Features heavily used by one group but not the other

**Timing differences:**
- How quickly do they take key actions?
- How frequently do they engage?
- Do they use the product in bursts or consistently?

**Path differences:**
- Do successful users follow a specific sequence?
- Where do unsuccessful users drop off?

### Step 5: Present Findings

Structure output as:

**Segment Comparison: [Segment A] vs [Segment B]**

**Key Differentiators**
| Behavior | Segment A | Segment B | Difference |
|----------|-----------|-----------|------------|
| Feature X usage | 80% | 20% | **4x higher** |
| Time to first action | 2 hours | 3 days | **36x faster** |
| Sessions per week | 5.2 | 1.3 | **4x more frequent** |

**Unique to Segment A** (successful users):
- Event X (used by 70% of Segment A, 5% of Segment B)
- Event Y (used by 60% of Segment A, 10% of Segment B)

**Unique to Segment B** (unsuccessful users):
- Event Z (error or friction signal)

**Recommended Actions**
1. Guide more users to [key differentiator behavior]
2. Reduce friction around [drop-off point]
3. Highlight [underused feature] to [unsuccessful segment]

**Supporting Data**
- Include flow diagrams showing path differences
- Link to Mixpanel reports for each segment

## Example Interaction

**User**: "Why do some trial users convert and others don't?"

**Assistant**:
1. Asks: "What's your trial length and what defines conversion?"
2. User says: "14-day trial, conversion = upgraded to paid"
3. Runs queries:
   - Segment A: Users who converted (last 90 days)
   - Segment B: Users who didn't convert (last 90 days)
   - Compares events, features, timing
4. Finds differentiators:
   - **Converters use "Export" feature 5x more** (80% vs 15%)
   - **Converters invite teammates 3x more** (60% vs 20%)
   - **Converters hit "wow moment" within 48h** (median: 1 day vs 8 days)
5. Outputs:
   - **High priority**: Accelerate time to "wow moment" (onboarding flow)
   - **Medium**: Encourage Export feature trial (add tooltip, email)
   - **Low**: Prompt team invites earlier (could drive virality)

## Common Journey Comparisons

**Trial converters vs. non-converters**
- What features drive conversion?
- When do converters realize value?
- What blockers prevent conversion?

**Retained vs. churned**
- What habits do retained users form?
- Where do churned users disengage?
- What's the "point of no return" (predict churn)?

**Power users vs. casual**
- What makes someone a power user?
- Can casual users be activated into power users?
- What features unlock high engagement?

**High LTV vs. low LTV**
- Which features correlate with revenue?
- Do high LTV users have different onboarding?
- What expansion behaviors exist?

## Best Practices

- **Use large enough samples** (100+ users per segment for confidence)
- **Control for time** (compare users in same time window)
- **Look for causal patterns** (not just correlation)
- **Focus on early behaviors** (leading indicators, not lagging)
- **Quantify differences** (2x, 5x, 10x — not just "higher")
- **Suggest experiments** (test whether guiding users to do X improves outcomes)
