# Ideas

Two linked candidates. **01 is the signal; 02 is who uses it and how we measure that.** Each is written as a menu of methods and success tiers, not a fixed plan. New ideas start from [`_TEMPLATE.md`](_TEMPLATE.md).

| # | Idea | Role | Core question | Final metric |
|---|---|---|---|---|
| 01 | [Cross-asset leadership](01-cross-asset-leadership.md) | Signal engine | Who leads whom after removing the market, and does leadership rotate predictably? | OOS rank IC; dynamic vs static leadership (DM test) |
| 02 | [Execution-timing signal](02-execution-timing-signal.md) | Application / pitch | Does 01's drift forecast cut an execution desk's slippage vs a plain schedule? | Execution improvement (bps vs benchmark), per-day distribution |

```
ticks (q) ──► 01: market / idiosyncratic split + leadership ──► drift forecast
                                                                    │
                                     02: execution desk uses it to time child orders
                                                                    │
                                          slippage saved vs TWAP / arrival (Java sim)
```

## Retired ideas

Earlier candidates (clock and latency, regime / reflexivity, execution footprints, realized vol, adverse-selection skew) were removed on 2026-10-01 to focus the project; see `docs/DECISIONS.md`. They're still in the git history:

```bash
git log --diff-filter=D --name-only -- ideas/
```
