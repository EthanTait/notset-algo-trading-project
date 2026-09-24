# 04 · Execution-algo footprints

**Status:** candidate (highest originality, highest risk)
**One line:** detect the periodic footprints of TWAP/VWAP-style execution algorithms in the tape, and test whether an unfinished parent order predicts continued drift.

## Question

Large orders get sliced into child orders, often at regular intervals or in repeated sizes. If we can detect a parent order in progress, it will probably keep pushing price in the same direction until it finishes.

## Why tick data

The footprint is in exact print times and sizes. 1-min bars erase it.

## Candidate hypotheses

- **H1:** The tape contains statistically significant periodicities / repeated-size clusters beyond what a Poisson null produces.
- **H2:** After detection, returns drift in the parent order's direction over the next minutes, more than in matched non-detection periods.
- **Null result:** detections occur at the Poisson-null rate, or there's no post-detection drift.

## Methodology menu

| Step | Options |
|---|---|
| Detection | spectral analysis / periodogram of same-side arrival counts · autocorrelation of inter-trade times · repeated-size clustering (same size ± tolerance, same side, regular spacing) |
| Null | Poisson arrivals with the same intraday intensity · shuffled inter-arrival times |
| Direction / size estimate | side of detected children, cumulative child volume, elapsed duration |
| Test | event study of post-detection returns vs matched controls |
| Signal | continuation position while the footprint is active; exit when it stops |

## Pipeline mapping

- **q:** per-side trade streams, size-bucketed sequences, rolling arrival counts.
- **Python:** spectral / clustering detection, event studies, nulls.
- **Java (if needed):** online detector and continuation strategy (a natural streaming state machine).

## Success tiers

1. **Descriptive:** detection rate materially above the Poisson null.
2. **Predictive:** post-detection drift significant against matched controls (bootstrap by day).
3. **Economic:** continuation strategy after costs.

## Known traps / artefacts

- Exchange-side batching, funding timestamps and scheduled events create periodicity that isn't an execution algo. Mask them.
- Round-number sizes are common for unrelated reasons, so require spacing *and* size together.
- Risk that detections are too rare to test. Do a quick feasibility pass first.

## Data needs

- Trades only, 1-2 symbols, a few weeks for a feasibility pass.

## Scope ladder

- **Minimum viable:** BTC, 1 week, periodogram vs Poisson null.
- **Extension:** size clustering, event study with controls.
- **Stretch:** online detector in Java, continuation strategy.

## Links to lectures and coursework

- Lecture 3: order types (iceberg orders); Lecture 4: trade features, backtest method.
- Lecture 4 project examples: #6 Liquidations (identifying entry prices from volume), which is a similar "reverse-engineer participants" spirit.

## Open questions

- Does Binance's trade feed show enough size granularity to identify children?
