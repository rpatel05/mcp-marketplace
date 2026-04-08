---
name: analyze-report
description: Deep-dive analysis of a single Mixpanel report to explain trends, detect anomalies, identify likely drivers, and provide actionable recommendations
---

# analyze-report

Deep-dive a single Mixpanel report for comprehensive insights.

## When to Use

- A metric moved unexpectedly
- Need to explain a trend for stakeholders
- Investigating conversion drop-offs
- Understanding segment differences

## Instructions

### Step 1: Retrieve Report Data

Run the report query to get current data.

### Step 2: Identify Patterns

**For Insights reports:**
- Trend direction (up, down, flat)
- Magnitude of change
- Segment differences
- Outlier days/weeks

**For Funnels:**
- Conversion rate overall
- Drop-off steps
- Time to convert
- Segment performance

**For Retention:**
- Day 1, Day 7, Day 30 rates
- Cohort differences
- Recent vs. historical

### Step 3: Detect Anomalies

- Sudden spikes or drops (>20% change)
- Days with unusual patterns
- Segment divergence

### Step 4: Explain Drivers

Look for:
- Seasonality (day of week, time of day)
- Recent product changes
- External events
- Data quality issues

### Step 5: Provide Recommendations

Present structured analysis:

**Report Analysis: [Report Name]**

**Summary**
- Current value: [X]
- Change: [↑↓X%] vs. [period]

**Key Findings**
1. [Most important insight with data]
2. [Second most important]
3. [Third most important]

**Anomalies Detected**
- [Date/Segment]: [Description]

**Likely Drivers**
- [Factor 1]: [Evidence]
- [Factor 2]: [Evidence]

**Recommendations**
1. [p0] [Actionable next step]
2. [p1] [Actionable next step]

**Report Link:** [URL]

## Best Practices

- Include specific numbers and percentages
- Compare to benchmarks or historical periods
- Segment analysis when relevant
- Link supporting evidence
- Make recommendations actionable
