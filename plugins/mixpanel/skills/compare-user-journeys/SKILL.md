---
name: compare-user-journeys
description: Side-by-side comparison of two user groups (converted vs. churned, power users vs. casual, etc.) to surface behavioral differences and identify what drives success
---

# compare-user-journeys

Compare behavioral patterns between two user segments.

## When to Use

- Understanding what converts users
- Identifying power user behaviors
- Investigating why users churn
- Finding activation patterns

## Input

**Segment A:** (e.g., "Users who converted")
- Cohort ID or filter criteria

**Segment B:** (e.g., "Users who didn't convert")
- Cohort ID or filter criteria

**Time period:** Date range to analyze

## Analysis Steps

### Step 1: Get Behavioral Data

For each segment:
- Top events performed
- Event frequency
- Time to key milestones
- Feature adoption

### Step 2: Identify Differences

Compare:
- Events unique to one segment
- Frequency differences (Segment A does X 5x more)
- Sequence patterns
- Time-to-value differences

### Step 3: Find Correlations

What behaviors predict success:
- Leading indicators (happen before conversion)
- Magnitude of difference
- Statistical significance

## Output Format

**User Journey Comparison**

**Segment A:** [Description] (N=[count])
**Segment B:** [Description] (N=[count])

**Key Behavioral Differences**

| Behavior | Segment A | Segment B | Difference |
|----------|-----------|-----------|------------|
| [Event/Pattern] | [Metric] | [Metric] | [%] |

**Unique to Segment A:**
- [Behavior]: [Frequency/pattern]

**Unique to Segment B:**
- [Behavior]: [Frequency/pattern]

**Leading Indicators of Success**
1. [Behavior]: [Evidence]
2. [Behavior]: [Evidence]

**Recommendations**
1. [Drive behavior X to increase conversion]
2. [Reduce behavior Y to prevent churn]

## Best Practices

- Ensure sample sizes are adequate (100+ users)
- Look for *actionable* differences
- Distinguish correlation from causation
- Segment by acquisition source if relevant
- Include statistical significance
