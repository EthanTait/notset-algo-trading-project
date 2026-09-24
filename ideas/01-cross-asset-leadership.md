# 01 · Cross-asset leadership, net of market flow

**Status:** candidate (current favourite, not committed)
**One line:** among crypto perps, who leads whom *after removing the market*, does leadership rotate predictably, and does knowing today's leader beat assuming "BTC always leads"?

## Question

Everyone knows BTC moves the market. That part is the common factor, and it's priced almost instantly. The open question is the **idiosyncratic** part: when one coin gets its own flow (news, a sector rotation, a large buyer), does that information spread to related coins with a lag, and is the leader identity stable enough to forecast?

The creative piece is decomposing **both price and order flow** into market and idiosyncratic components, which gives four quadrants:

```
                 MARKET component             IDIOSYNCRATIC component
PRICE  (mid ret) m(t)   e.g. PC1 / basket     e_i = r_i - beta_i(t) * m
FLOW   (imbal.)  F(t)   cross-sectional avg   u_i = f_i - gamma_i(t) * F
```

The headline result is *which quadrant the lead lives in*. A natural hypothesis: common flow is priced instantly, while idiosyncratic flow spreads with a lag.

## Why tick data

Leads between liquid perps are plausibly measured in ms to seconds. At 1-min bars they are averaged away, apart from slow effects. Asynchronous trading also creates the Epps effect (correlations shrinking at high frequency), which you can only handle properly with the raw timestamps.

## Candidate hypotheses

- **H1:** Idiosyncratic flow in the leader (u_i) predicts the follower's idiosyncratic return (e_j) at a lag.
- **H2:** Common flow F adds nothing once contemporaneous returns are controlled for.
- **H3:** Leadership rankings persist from one window to the next more than a surrogate null does.
- **H4:** A dynamic-leadership forecast beats a static "BTC leads everything" forecast out of sample.
- **Null result:** leadership rankings are no more persistent than under a circular time-shift null, and/or the dynamic model fails the Diebold-Mariano test against the static one.

## Methodology menu

| Step | Options |
|---|---|
| Common grid | fixed bins (100ms / 250ms / 1s) · refresh-time sampling · Hayashi-Yoshida (no grid, pairwise) |
| Price input | BBO mid · microprice (for large-tick coins) |
| Market factor m | rolling PC1 (Time Series Econometrics) · BTC alone · equal- or volume-weighted basket · market + sector factors |
| Betas | rolling OLS · Kalman-filtered time-varying beta (Time Series Econometrics) |
| Flow f_i | Lecture 4 trade imbalance (EWMA, volume-normalized) · signed volume · order-flow imbalance from BBO changes |
| Leadership per window | lagged cross-correlation of residuals · VAR / Granger coefficients · Hasbrouck information share · PageRank / net out-degree on the lead graph |
| Leadership forecast | persistence only · regression on window features (idio volume share, relative vol, spread) · rank classifier |
| Signal → position | follower residual forecast `E[e_j(t+h)] = sum_i b_ij * u_i(t)`, held **market-neutral**: `w_j` in follower, `-w_j * beta_j` in the market proxy |

Estimation windows (e.g. 1h, stepped every 15m) give thousands of windows over a few months. That, plus residualization (cascades are mostly market-factor moves), is the main defense against the event trap.

## Pipeline mapping

- **q:** HDB of trades + BBO; grid construction (fixed / refresh-time); rolling flow features via `wj`; residual panels stored back to the HDB.
- **Python:** PCA, Kalman betas, VAR / cross-correlations per window, leadership ranking, forecasting, evaluation.
- **Java (if needed):** market-neutral strategy replay with decision latency, hedge latency and fees; the leader/follower book is a natural stateful strategy.

## Success tiers

1. **Descriptive:** leadership exists beyond artefacts (passes surrogate and liquidity-matched nulls) and rank persistence beats the null.
2. **Predictive:** daily OOS rank IC of the follower-residual forecast is > 0 in most days; dynamic beats static under the DM test.
3. **Economic:** market-neutral PnL survives costs and some latency. (Probably not at retail fees; reporting where it dies is fine.)

## Known traps / artefacts

- **Stale prices / non-trading** (Lo-MacKinlay): less liquid coins *look* like followers. Controls: use BBO mids, and compare each pair against a liquidity-matched null.
- **Tick size:** large-tick coins' mids move in discrete jumps, which looks like lag. Control: microprice.
- **Epps effect:** correlations collapse on too fine a grid. Report results across grid sizes.
- **Too few names:** 5 coins make a thin graph. See data needs.
- **Timestamps:** stay within one venue (Binance) for ms-level claims.

## Data needs

- Binance USD-M trades + BBO, **~10-15 perps across sectors** (majors, L1s, memes, DeFi), 2-3 months. No L2 needed.
- Optional: Hyperliquid trades for a cross-venue price-discovery extension, at second horizons only.

## Scope ladder

- **Minimum viable:** 5 homework symbols, 1-2 weeks of ticks, fixed grid, BTC-only market factor, cross-correlation leadership, persistence test.
- **Extension:** full universe, PCA + Kalman decomposition, four-quadrant result, DM test against static.
- **Stretch:** sector factor layer, market-neutral Java replay with latency, cross-venue information shares.

## Links to lectures and coursework

- Lecture 4: cross-correlation block structure, "price move of a correlated asset" as a feature, trade imbalance, mid-to-mid PnL, sliding IS/OOS.
- Lecture 4 project examples: #4 Market Regime (market correlation as a feature), #8 Pair Cointegration (a relative).
- MSQF: factor models + IS/OOS (Quant Methods); PCA, Kalman, VAR (Time Series Econometrics).

## Open questions

- Grid choice, and whether results hold across it
- Should the market factor include the leader itself (circularity)?
- Window length vs number of windows trade-off
