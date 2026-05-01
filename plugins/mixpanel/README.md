# Mixpanel Analytics Plugin

Expert product analytics skills for Mixpanel. This plugin provides comprehensive analysis, instrumentation, and data governance capabilities.

## Skills Overview

### Core Analytics
- **create-report**: Build Mixpanel Insights, Funnels, Flows, Retention, and other reports from natural language
- **analyze-report**: Deep-dive analysis of any Mixpanel report with trend detection and recommendations
- **create-board**: Assemble boards (dashboards) from requirements
- **analyze-board**: Comprehensive board analysis with cross-report insights

### Experiments
- **analyze-experiment**: Full experiment analysis with statistical rigor
- **monitor-experiments**: Track all running experiments

### Instrumentation
- **add-analytics-instrumentation**: End-to-end workflow to instrument a PR, branch, or feature
- **diff-intake**: Analyze code changes for analytics opportunities
- **discover-event-surfaces**: Identify what events should be tracked
- **instrument-events**: Generate tracking plan and implementation code
- **discover-analytics-patterns**: Map existing tracking patterns in your codebase

### Data Governance
- **audit-lexicon**: Review Lexicon for data quality issues
- **update-lexicon**: Bulk update event and property metadata
- **taxonomy**: Generate and enforce event/property naming conventions

### Reliability & Errors
- **diagnose-errors**: Investigate errors across custom error events to triage what's broken
- **monitor-reliability**: Proactive reliability health check with KPI trends and page health scores

### Account Health (B2B)
- **analyze-account-health**: Analyze B2B account usage, engagement, risk signals, and expansion opportunities using Group Analytics

### Session Replay
- **debug-replay**: Convert bug reports into numbered repro steps
- **replay-ux-audit**: Synthesize friction points across multiple replays

### Briefings
- **daily-brief**: Morning briefing of key changes
- **weekly-brief**: Weekly recap for team sharing

### Insights
- **discover-opportunities**: Find product opportunities across analytics, feedback, and replays
- **compare-user-journeys**: Side-by-side comparison of user segments
- **analyze-feedback**: Synthesize customer feedback themes

## Installation

See the [main README](../../README.md) for installation instructions.

## MCP Server

This plugin requires the Mixpanel MCP server. Install it with:

```bash
npx @mixpanel/mcp-server
```

## License

MIT
