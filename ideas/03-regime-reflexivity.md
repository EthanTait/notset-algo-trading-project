# 03 · Regime: when does flow mean-revert and when does it trend?

**Status:** candidate
**One line:** predict whether short-horizon order flow will be followed by reversal or continuation, using tick-only measures of market self-excitation.

## Question

Lecture 4 found that 1-min trade imbalance **mean-reverts**, so the signal had to be flipped. That can't hold all the time: squeezes and cascades are continuation. If we can forecast the regime, the same feature becomes a better signal (flipped or not, sized up or down).

The creative input is a **Hawkes process** on trade arrivals. Its *branching ratio* estimates the share of activity triggered by earlier activity rather than by new information. It's a "reflexivity meter": high values should mean herding and continuation.

## Why tick data

Hawkes estimation needs individual event times. Book refill speed after a sweep and trade-size distributions also need ticks.

## Candidate hypotheses

- **H1:** A higher branching ratio (or other tick features) predicts positive autocorrelation of returns / flow over the next window.
- **H2:** Conditioning the Lecture 4 imbalance signal on the predicted regime improves OOS IC over the unconditional signal.
- **Null result:** regime forecasts no better than "last window's regime" (persistence benchmark) under DM.

## Methodology menu

| Step | Options |
|---|---|
| Regime label (target) | sign of next-window return autocorrelation at lag k · variance ratio > 1 vs < 1 · sign of imbalance → return correlation |
| Tick features | Hawkes branching ratio (univariate or buy/sell bivariate) · book refill time after sweeps · spread level + dynamics · trade-size distribution (share of large prints) · burstiness of arrivals |
| Bar-level features | realized vol, vol-of-vol, cross-asset correlation (Lecture 4's regime feature list) · GARCH conditional vol (Time Series Econometrics) |
| Model | logistic · gradient boosting · simple threshold rules · HMM on features |
| Use of forecast | flip / scale the imbalance signal · switch between momentum and reversal signals |

## Pipeline mapping

- **q:** event tables for Hawkes fitting (arrival times by side), window aggregates, sweep detection.
- **Python:** Hawkes estimation (e.g. the `tick` library, or our own MLE), regime labels, classifiers, conditional-signal evaluation.
- **Java (if needed):** the regime-switching strategy replay: regime state, switching logic and cooldowns are all stateful.

## Success tiers

1. **Descriptive:** regime labels are persistent enough to be forecastable; the branching ratio tracks known episodes.
2. **Predictive:** OOS classification accuracy/calibration beats the persistence benchmark (DM for probabilistic loss, calibration buckets).
3. **Economic:** regime-conditioned imbalance signal beats unconditional after costs.

## Known traps / artefacts

- Regime labels that use future data in the features (look-ahead). The label window must be strictly after the feature window.
- Hawkes fits are sensitive to intraday seasonality in activity; de-seasonalize the baseline intensity.
- Episodes (squeezes) are few. Evaluate on many rolling windows and apply the per-day metrics from `EVALUATION.md`.

## Data needs

- Trades (+ BBO for spread/refill) for 2-5 symbols, 1-3 months. L2 only if we use book refill.
- Optional: liquidations + open interest (on demand) as validation of "reflexive" episodes.

## Scope ladder

- **Minimum viable:** BTC, univariate Hawkes per hour, persistence vs Hawkes-based regime forecast.
- **Extension:** bivariate buy/sell Hawkes, more features, conditional signal evaluation.
- **Stretch:** cross-asset regime (combine with 01) and a Java regime-switching replay.

## Links to lectures and coursework

- Lecture 4: autocorrelation slides (1-3 min mean reversion), imbalance sign flip; project examples #3 Momentum, #4 Market Regime, #4 Pump and Dump.
- MSQF: GARCH (Time Series Econometrics); calibration testing in the style of VaR backtests (Risk Management).

## Open questions

- What window length defines a regime (5m? 1h?)
- Is the branching ratio stable enough to estimate on short windows?
