# Gotchas

The gotchas section carries the most signal in any skill. It usually doesn't exist on day one. Add a line each time the agent trips on something, so the section grows from real failures. A good gotcha is a specific fact that looks wrong or surprising.

Keep gotchas in `SKILL.md`, not a reference file, even in a skill split across many files: the agent needs them before it hits the situation and won't know to look for them.

## Examples

The section grows from real failures, one line each time the agent trips:

```markdown
# Billing Lib
How to use the internal billing library.

## Gotchas
- Proration rounds DOWN, not to the nearest cent.
- Test mode skips the `invoice.finalized` hook.
- Idempotency keys expire after 24h, not 7d.
- Refunds need the charge ID, not the invoice ID.
```

More gotchas of the right shape, each a specific fact that looks wrong or surprising:

- "The `subscriptions` table is append-only. The current row has the highest `version`, not the latest `created_at`."
- "The gateway calls it `@request_id` and the billing service calls it `trace_id`. They are the same value."
- "Staging returns 200 even when the webhook did not process. Check `payment_events` for the real state."
