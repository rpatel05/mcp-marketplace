---
name: analyze-experiment
description: Designs A/B tests with proper metrics and variants, analyzes running or completed experiments, and interprets results with statistical rigor. Use when setting up experiments, checking experiment status, analyzing results, or making ship decisions.
---

# Experiment Analyst

Perform comprehensive, detailed deep-dive analysis of experiments to make data-driven ship/no-ship decisions.

## When to Use

- Analyzing completed experiment results for ship decisions
- Checking on running experiment progress and early signals
- Understanding why an experiment succeeded or failed
- Investigating unexpected results or segment-level effects

## Analysis Philosophy

**Be comprehensive, not brief:**
- Include specific numbers, percentages, and data points
- Explain statistical meaning AND business implications in plain language
- Cover all metrics (primary, secondary, guardrails) with actual values

## Instructions

### Step 1: Retrieve Experiment

Get experiment details including:
- Name, description, and state
- Start/end dates and duration
- Variants: names, traffic allocation
- Metrics: primary, secondary, guardrails

### Step 2: Check Data Quality

Assess:

**Traffic Balance:**
- Report actual traffic split per variant
- Flag Sample Ratio Mismatch (SRM) if detected

**Sample Size:**
- Report total users per variant
- Flag if <100 users (insufficient)
- Flag if 100-1000 users (directional only)
- Need 1000+ per variant for confident decisions

**Statistical Power:**
- Calculate power to detect meaningful effect size
- Recommend extending if underpowered (<80%)

### Step 3: Analyze Primary Metric

Extract and report:
- Control baseline
- Treatment performance
- Absolute lift
- Relative lift (%)
- P-value with interpretation
- Confidence interval

**Interpret:**
- ✅ Significant: p < 0.05 and CI doesn't include 0
- ⚠️ Trending: 0.05 < p < 0.15
- ❌ No effect: p ≥ 0.15

### Step 4: Analyze Secondary Metrics & Guardrails

For each metric:
- Report performance and significance
- Identify unintended consequences
- Flag any regressions

### Step 5: Segment Analysis

Test key segments:
1. Platform (iOS, Android, Web)
2. User tenure (new vs. established)
3. Plan type (free vs. paid)
4. Geography

Present as markdown tables showing:
- Segment
- Control/Treatment rates
- Lift
- Significance
- % of total exposures

### Step 6: Synthesize and Recommend

Present structured analysis with:

**Overview:**
- Hypothesis
- Duration
- Sample size

**Data Quality:**
- Traffic balance
- Sample adequacy
- Statistical power

**Primary Metric:**
- Results table with lift, CI, p-value
- Interpretation

**Secondary Metrics:**
- Guardrails status
- Unintended consequences

**Segment Analysis:**
- Breakdown tables
- Key findings

**Recommendation:**
- ✅ SHIP / ⚠️ ITERATE / ❌ ABANDON / 🔄 NEED MORE DATA
- Rationale (1-5 points)
- Known risks
- Next steps

**Key Takeaways:**
- 3-5 actionable insights

## Best Practices

- Include ALL data with specific numbers
- Report confidence intervals, not just p-values
- Distinguish statistical vs. practical significance
- Check for Simpson's Paradox in segments
- Don't make recommendations without adequate power
