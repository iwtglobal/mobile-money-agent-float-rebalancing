# Agent Float Rebalancing Checklist

For the float desk and master-agent support. Replace band numbers with your scheme values.

## When a shortage alert fires

1. Confirm the leaf agent's current float and open cash-in queue (if any).
2. Identify the parent (`parent_agent_id`) and check parent float against its own min band.
3. If parent has headroom: guide or auto-approve cascade top-up to bring leaf above min + buffer.
4. If parent is also short: escalate to regional / treasury; do not leave the leaf hanging.
5. Confirm both ledgers show the same `topup_txn_id` and amount.
6. Clear the alert only after float recovers above the clear threshold.

## Daily float desk

- [ ] Count of agents below min band at open of day
- [ ] Cascade top-ups failed for insufficient parent float
- [ ] Agents with alerts older than the SLA (e.g. 30 minutes)
- [ ] Masters with unusually high unused float while children are short

## Weekly

- [ ] Review band settings for agents that alert more than N times
- [ ] Spot-check fees on cascade top-ups against the published tariff
- [ ] Confirm settlement batches closed for float sales
