# Mobile Money Agent Float Rebalancing

How a **mobile money platform** keeps agent float usable: when a shop runs short of e-float for cash-in, how cascade top-up from a master agent works, and how shortage alerts should reach the right person before customers walk away. Written for operators evaluating [EVD System](https://evdsystem.com/) by [MoboGage](https://evdsystem.com/about-mobogage/).

---

## What agent float is

**Agent float** is the electronic balance an agent uses to accept cash from customers (cash-in) and pay cash out (cash-out). Cash-in drains float; cash-out adds float and drains physical cash. When float is too low, the agent cannot cash customers in. When float is too high and cash is low, they cannot cash customers out.

Rebalancing moves float (and sometimes cash) so agents stay inside working bands instead of hitting zero mid-day.

## Why rebalancing matters

A dead float balance at a busy agent is lost volume and a reputation hit. Centralised top-up from the operator treasury does not scale to thousands of agents. Most schemes use a hierarchy: master agent or aggregator holds a larger float pool and pushes or sells float down to leaf agents.

## A process that has worked

```
Leaf agent float < min_band
        |
        v
Shortage alert -> Master agent / aggregator
        |
        +-- cascade top-up (float transfer) --> Leaf agent
        |
        +-- if master also short --> Regional / treasury top-up
```

**Min / max bands** are set per agent tier and location. A rural agent may run a lower max than a city mall kiosk.

**Cascade top-up** is a float transfer from parent to child, usually paid for by the child (or settled later). It should be real-time on the ledger, with a receipt both sides can see.

**Shortage alerts** fire before zero: for example when float drops below 20% of the agent's typical daily cash-in (**illustrative** rule). SMS to the agent and a push to the master agent's app are enough for most networks.

## Worked example (figures are illustrative)

Agent L12 has a min band of **2,000.00** units and a max of **15,000.00**. At 11:40 the float sits at **1,650.00** after a string of cash-ins. The platform sends a shortage alert to L12 and to master agent M3.

M3 has **42,000.00** available. M3 approves a cascade top-up of **8,000.00** to L12. Ledger entries:

- Debit M3 float **8,000.00**
- Credit L12 float **8,000.00**
- Fee line of **40.00** (**illustrative**) if the scheme charges a transfer fee

L12 is back above the min band. The alert clears. If M3 had been below its own min, the alert would have escalated to the regional float desk instead of failing silently at the leaf.

## Fields ops will ask for

| Field | Why |
|-------|-----|
| `agent_id`, `parent_agent_id` | Hierarchy for cascade |
| `float_balance`, `min_band`, `max_band` | Working limits |
| `last_shortage_alert_at` | Avoid alert spam |
| `topup_txn_id`, `amount`, `fee` | Reconciliation |
| `initiator` | Agent self-request, master push, treasury |
| `settlement_batch_id` | When float sales settle overnight |

## Things that go wrong

**Alert fatigue.** Firing at every 1-unit dip trains agents to ignore SMS. Use hysteresis: alert on cross-below-min, clear only after float recovers above min + buffer.

**Master hoarding.** Some masters delay top-ups to keep leaf agents dependent. Publish SLA timers and let leaf agents request from an alternate parent when the primary is idle too long.

**Cash vs e-float mismatch.** Rebalancing e-float does not fix a cash shortage. Pair float alerts with a simple cash-on-hand check in the agent app where your market allows it.

A checklist for the float desk is in [docs/float-rebalancing-checklist.md](./docs/float-rebalancing-checklist.md).

---

## FAQ

**What is agent float rebalancing?**
It is moving electronic balance between agents (or from treasury) so shops stay inside working min/max bands and can keep serving cash-in and cash-out.

**What is cascade top-up?**
A float transfer from a parent (master/aggregator) down to a leaf agent, recorded on both ledgers with a clear receipt.

**When should a shortage alert fire?**
Before float hits zero—typically when balance crosses below the agent's min band—and it should reach both the leaf agent and the parent.

**Who pays for a cascade top-up?**
Usually the leaf agent (or it is settled in the next commission cycle). Spell the fee out on the receipt.

**Does MoboGage work with multi-tier agent and reseller networks?**
Yes. EVD System and the wider MoboGage stack are built around multi-tier distribution for vouchers and related float-style balances.

---

## Glossary

| Term | Meaning |
|------|---------|
| Agent float | E-balance an agent uses for customer cash-in / cash-out |
| Min / max band | Working limits that trigger alerts or block further imbalance |
| Cascade top-up | Parent-to-child float transfer |
| Master agent | Aggregator that holds a larger float pool for leaf agents |
| Shortage alert | Notice that float has dropped below the min band |
| Treasury top-up | Operator-level float injection into the hierarchy |

## Further reading

- [Mobile money platform with merchant payment](https://evdsystem.com/mobile-money-platform-with-merchant-payment/) on evdsystem.com
- [Multi-tier reseller voucher distribution](https://evdsystem.com/evd-software/multi-tier-reseller-voucher-distribution/) on evdsystem.com
- Related repo: [voucher-distributor-wallet-topup](https://github.com/iwtglobal/voucher-distributor-wallet-topup)

## License

MIT. See [LICENSE](./LICENSE). Educational material only, not legal or regulatory advice.
