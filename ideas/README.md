# Ideas

One file per candidate thesis. None is committed yet. Each is written as a **menu of methods and success tiers**, so we can narrow, combine or drop ideas after a first pass on the data. New ideas start from [`_TEMPLATE.md`](_TEMPLATE.md).

| # | Idea | Core question | Needs ticks because | Risk | Java role | Status |
|---|---|---|---|---|---|---|
| 01 | [Cross-asset leadership](01-cross-asset-leadership.md) | Who leads whom after removing the market, and does leadership rotate predictably? | ms-to-s leads; asynchronous trading | Med | market-neutral leader/follower replay | **favourite** |
| 02 | [Clock and latency](02-clock-and-latency.md) | How do Lecture 4 signals change with sampling clock and decision latency? | trade/volume clocks; sub-bar latency | Low | incremental "production" features | candidate |
| 03 | [Regime / reflexivity](03-regime-reflexivity.md) | When does flow mean-revert vs trend, and can a Hawkes branching ratio tell us? | event times for Hawkes | Med | regime-switching strategy | candidate |
| 04 | [Execution footprints](04-execution-footprints.md) | Can we detect TWAP/VWAP children and trade the unfinished parent? | exact print times/sizes | High | online detector | candidate |
| 05 | [Realized vol from ticks](05-realized-vol-microstructure.md) | Does noise-robust RV improve vol forecasts? | signature plots | Low | probably none | candidate |
| 06 | [Adverse selection / protective skew](06-adverse-selection-protective-skew.md) | Can cross-asset flow protect quotes from going stale? | quote staleness | High | maker simulation | **parked** |

## Natural combinations

- **01 + 03:** leadership strength as a regime variable, or regime-conditioned leadership.
- **02 + 03:** the regime forecast as the conditioning variable for the clock/latency grid.
- **01 + 05:** vol spillover as a slower, more robust form of leadership.

## How we choose

1. A 1-2 day feasibility pass on the homework 1-min data or a small tick sample for the top 2-3 ideas.
2. Pick the one whose **success tiers are measurable with data we can actually get** (see `docs/EVALUATION.md`).
3. Record the choice and the reasons in `docs/DECISIONS.md`, and fill in its pre-registration table.
