# 02 · Lecture 4 signals: which clock, and at what latency?

**Status:** candidate
**One line:** rebuild the Lecture 4 features on ticks and measure how their out-of-sample value changes with (a) the sampling clock and (b) decision latency.

## Question

Lecture 4 shows trade imbalance at Sharpe ~2.8 (3.6 filtered) mid-to-mid on 1-min bars, then lists why that's hard to realize: fees, spread, fill rate, alpha decay, and research features differing from production ones. Tick data lets us put numbers on those gotchas.

Two axes:
- **Clock:** calendar time vs trade-count time vs volume time. The time-change hypothesis (Clark; Ané-Geman) says returns look close to Gaussian in business time. Our HW1 found excess kurtosis ~184 for 1-min BTC returns, so there's a lot of room.
- **Latency:** how fast each feature's predictive value decays as the gap between signal and action grows (0 → 10ms → 100ms → 1s → 60s).

The output is a **grid** (feature × clock × latency) of OOS metrics.

## Why tick data

Both axes need it. Trade and volume clocks are built from individual prints, and latency below one bar can't be simulated from bars.

## Candidate hypotheses

- **H1:** In volume/trade time, returns are closer to Gaussian (lower kurtosis) and Lecture 4 features have higher OOS IC than in calendar time.
- **H2:** Most of the 1-min signal's value sits in the first seconds; OOS IC decays with latency at a rate we can estimate per feature.
- **H3:** Signals that look strong mid-to-mid lose most of their value after spread + taker fees.
- **Null result:** no clock beats calendar time under the DM test, or decay curves are flat (signal was not a microstructure effect).

## Methodology menu

| Step | Options |
|---|---|
| Clocks | calendar (100ms…60s) · trade-count bars · volume bars · dollar-volume bars |
| Features | Lecture 4 set: return EMA, trade imbalance, book imbalance (from BBO sizes) · plus cross-asset (BTC) versions |
| Transforms | winsorize · cbrt · EMA smoothing (Lecture 4 tail-reduction list) |
| Model | single-feature IC · ridge on the feature set (Lecture 4 project example #1) |
| Latency model | shift decision time by L · fill at mid / touch / touch + fee |
| Distribution tests | kurtosis, Jarque-Bera, QQ plots by clock; variance ratios (as in HW1) |

## Pipeline mapping

- **q:** build each clock's bars from ticks (`xbar` for calendar time, cumulative-volume buckets for volume time); feature tables per clock.
- **Python:** feature transforms, IC / ridge, latency sweeps (vectorized), plots.
- **Java (if needed):** realistic latency + fill replay for the best 1-2 signals, to check the vectorized latency sweep and give a "production feature" version computed incrementally.

## Success tiers

1. **Descriptive:** distribution comparison across clocks (a direct extension of HW1).
2. **Predictive:** OOS daily rank IC surface over clock × latency, with DM tests between clocks.
3. **Economic:** after-cost Sharpe surface; latency at which each signal becomes worthless.

## Known traps / artefacts

- Volume clocks sample more in busy periods, so compare metrics per unit of *calendar* time as well, or the comparison is unfair.
- Overlapping forward windows (Lecture 4 warning): use non-overlapping sampling or Newey-West.
- Bid-ask bounce in trade prices: use mids for returns.

## Data needs

- Trades + BBO for the 5 homework symbols (or even just BTC + ETH), 1-2 months. No L2 needed.

## Scope ladder

- **Minimum viable:** BTC only, 3 clocks, 2 features, latency sweep in Python.
- **Extension:** all 5 symbols, full feature set + ridge, after-cost surfaces.
- **Stretch:** Java replay with incrementally computed features to measure the research-vs-production gap directly.

## Links to lectures and coursework

- Lecture 4: feature construction, desirable feature properties (zero mean, no skew, thin tails), tail reduction, mid-to-mid PnL, the 1-min-delay slide, "Gotchas: real-time calculation".
- HW1: vol scaling, variance ratios, kurtosis across horizons.
- MSQF: IS/OOS discipline (Quant Methods).

## Open questions

- Pick bar sizes so each clock has a similar number of observations per day?
- Does this pair naturally with 03 (regime) as the conditioning variable?
