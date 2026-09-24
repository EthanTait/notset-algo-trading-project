# 05 · Realized volatility from ticks

**Status:** candidate (safest, most standard)
**One line:** measure intraday volatility properly from ticks despite microstructure noise, then forecast it.

## Question

Realized variance from very fine returns is biased upward by bid-ask bounce and discreteness (microstructure noise). Too coarse, and you throw away information. Where's the optimum for crypto perps, do noise-robust estimators help, and do better measurements give better vol forecasts?

## Why tick data

Signature plots and noise-robust estimators need the finest-grained prices available.

## Candidate hypotheses

- **H1:** Signature plots show noise dominating below some sampling interval; that interval differs by coin with tick size and liquidity.
- **H2:** Noise-robust RV (two-scale, realized kernel) used as the HAR input improves OOS vol forecasts compared with 5-min RV.
- **Null result:** no forecast improvement under DM with QLIKE loss.

## Methodology menu

| Step | Options |
|---|---|
| Price | trade prices · BBO mid · microprice |
| Estimators | RV at many intervals (signature plot) · two-scale RV · realized kernel · bipower variation (jump-robust) |
| Forecast model | HAR-RV · HAR with jumps · GARCH on bars as benchmark (Time Series Econometrics) |
| Loss | QLIKE · MSE on log RV |
| Extension | cross-asset: does BTC RV forecast alt RV (vol spillover)? |

## Pipeline mapping

- **q:** multi-interval returns from ticks, daily/hourly RV tables.
- **Python:** estimators, HAR/GARCH, forecast evaluation.
- **Java:** probably not needed. If used, a vol-targeted execution replay.

## Success tiers

1. **Descriptive:** signature plots and the optimal-interval result per coin.
2. **Predictive:** DM test of HAR with robust RV vs HAR with 5-min RV vs GARCH.
3. **Economic:** vol-targeted position sizing on a simple strategy, or a VaR application (Risk Management backtests: Kupiec/Christoffersen).

## Known traps / artefacts

- Weekends and 24/7 trading: define "day" consistently (UTC).
- Jumps inflate RV. Separate them with bipower variation.

## Data needs

- Trades + BBO for 2-5 symbols, ideally longer history (3+ months) for HAR.

## Scope ladder

- **Minimum viable:** BTC signature plot + HAR with two RV inputs.
- **Extension:** all coins, robust estimators, jumps.
- **Stretch:** cross-asset vol spillover (links to 01).

## Links to lectures and coursework

- HW1: vol scaling across horizons, EWMA vol (this is its tick-level sequel).
- Lecture 4 group example name: "Realized Volatility Prediction".
- MSQF: GARCH (Time Series Econometrics), VaR backtesting (Risk Management).
