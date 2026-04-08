---
name: discover-event-surfaces
description: From a change_brief YAML, identifies candidate analytics events that should be tracked. Outputs prioritized event_candidates YAML for PM review.
---

# discover-event-surfaces

Analyze code changes and discover what analytics events should be tracked.

## Input

Consumes `change_brief` YAML from `diff-intake`.

## What it does

1. Reviews each changed file
2. Identifies user-facing interactions
3. Maps interactions to trackable events
4. Assigns priority (1=nice-to-have, 2=recommended, 3=critical)
5. Suggests event names and properties

## Output Format

```yaml
event_candidates:
  - event_name: Payment Method Selected
    priority: 3
    trigger: User selects payment method in checkout
    file: src/checkout/PaymentForm.tsx
    suggested_properties:
      - payment_method
      - checkout_step
```

## Usage

Invoked by `add-analytics-instrumentation` as Step 2.

Downstream: `instrument-events` consumes this YAML.
