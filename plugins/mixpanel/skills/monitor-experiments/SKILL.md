---
name: monitor-experiments
description: Triages all active and recently completed experiments, flagging which need attention, which are ready to ship, and which should be stopped
---

# monitor-experiments

Monitor all experiments and prioritize which need attention.

## What it does

1. Lists all active experiments
2. Lists recently completed experiments (past 7 days)
3. Checks status and data quality
4. Flags experiments needing attention
5. Prioritizes by urgency

## Output Format

**Experiment Monitor**

**🚨 Needs Immediate Attention**

- **[Experiment Name]**
  - Status: [Running/Completed]
  - Issue: [SRM detected / Guardrail regression / Ready to ship]
  - Action: [Specific next step]

**✅ Ready to Ship**

- **[Experiment Name]**
  - Primary: [↑X% lift, p<0.05]
  - Guardrails: All clear
  - Recommendation: Ship to 100%

**🔄 Continue Running**

- **[Experiment Name]**
  - Status: Trending positive but underpowered
  - ETA: [X more days for significance]

**🚩 Consider Stopping**

- **[Experiment Name]**
  - Status: No effect detected, adequate power
  - Recommendation: Stop and iterate

**📊 All Experiments**

| Name | Status | Primary Metric | Sample Size | Days Running |
|------|--------|----------------|-------------|---------------|
| [Name] | Active | [↑X%] | [N] | [X] |

## Best Practices

- Run weekly or daily during launch periods
- Prioritize experiments with data quality issues
- Flag experiments running >4 weeks
- Link to detailed analysis for each flagged experiment
