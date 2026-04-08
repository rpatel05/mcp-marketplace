---
name: diff-intake
description: Reads a PR or branch diff and outputs a structured change_brief YAML summarizing what changed, which files, and existing analytics patterns. First step in the instrumentation workflow.
---

# diff-intake

Analyze code changes (PR or branch diff) and produce a structured YAML summary for downstream instrumentation skills.

## What it does

1. Gets the diff (from PR or branch)
2. Categorizes changes (feat, fix, refactor, etc.)
3. Identifies user-facing vs internal changes
4. Scans for existing analytics patterns
5. Outputs `change_brief` YAML

## Output Format

Produces a YAML block:

```yaml
change_brief:
  primary: feat
  analytics_scope: high  # high, medium, low, none
  classification:
    types: [feat, refactor]
  file_summary_map:
    src/checkout/PaymentForm.tsx:
      layer: presentation
      summary: Added new payment method selection UI
      existing_instrumentation:
        - mixpanel.track('Payment Method Selected')
```

## Usage

Invoked by `add-analytics-instrumentation` as Step 1.

Downstream: `discover-event-surfaces` consumes this YAML.
