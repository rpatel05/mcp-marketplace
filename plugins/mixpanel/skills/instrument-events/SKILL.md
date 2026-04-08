---
name: instrument-events
description: From prioritized event_candidates YAML, generates a concrete tracking plan with exact code locations, Mixpanel SDK calls, and property definitions.
---

# instrument-events

Generate implementation-ready tracking plan from event candidates.

## Input

Consumes `event_candidates` YAML from `discover-event-surfaces`.

## What it does

1. Reviews existing analytics patterns in codebase
2. For each priority 3 event:
   - Determines exact file and function location
   - Generates Mixpanel SDK tracking call
   - Defines property schema
3. Outputs trackingPlan JSON

## Output Format

```json
{
  "trackingPlan": [
    {
      "eventName": "Payment Method Selected",
      "file": "src/checkout/PaymentForm.tsx",
      "function": "handlePaymentMethodChange",
      "code": "mixpanel.track('Payment Method Selected', { payment_method: method, checkout_step: 2 });",
      "properties": {
        "payment_method": "string (credit_card | paypal | apple_pay)",
        "checkout_step": "number"
      }
    }
  ]
}
```

## Usage

Invoked by `add-analytics-instrumentation` as Step 3 (final step).

This is the deliverable given to engineering for implementation.
