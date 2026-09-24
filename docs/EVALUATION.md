# Evaluation protocol

Shared by every idea. It exists because of two risks we flagged in ideation:

1. **Event trap:** a few crashes or squeezes produce most of the "signal", and the result is really about three days.
2. **Undefined success:** without a metric fixed in advance, the thesis drifts to whatever the data happened to show.

So the rule is: **each idea states its metrics in the table at the bottom before we look at out-of-sample results.** Changing them afterwards goes in `DECISIONS.md` with a reason.

## 1. Sample discipline

- **Walk-forward (sliding) in-sample / out-of-sample windows**, as in the Lecture 4 signal-optimization slide: fit on IS, evaluate on the next OOS block, slide forward. No shuffled cross-validation, because time series leak.
- A **final holdout** (e.g. the last 2-3 weeks) is left untouched until the method is frozen. We look at it once.
- Hyperparameters (windows, half-lives, thresholds) are chosen on IS only. These are the IS/OOS rules from MSQF Quant Methods.

## 2. Primary metric: per-day, rank-based, vol-normalized

- Forecast target: forward return over horizon h **divided by current EWMA vol**, so high-vol days don't dominate by scale alone.
- Metric: **rank IC** (Spearman correlation of signal against target) **computed per day**.
- Report: **median daily IC**, **% of days with IC > 0**, and the IC distribution, not only the pooled number. One crash day then counts as one observation.

## 3. Robustness checks (run for every headline number)

| Check | Why |
|---|---|
| Drop the top 5 days by realized vol, re-run | Is it just an event effect? |
| Block bootstrap by day for CIs | Intraday observations are not independent |
| Newey-West standard errors when forward windows overlap | Lecture 4: overlapping 5-min windows produce spurious autocorrelation |
| Split by regime (high/low vol, weekday/weekend, Asia/EU/US hours) | Is it stable, or does one regime drive it? |
| Winsorize or cbrt-transform features (Lecture 4 "reducing tails") | Keeps correlations from being driven by the tails |
| Placebo / surrogate null (e.g. circular time-shift of one series) | Separates real structure from artefacts like autocorrelation, stale prices or tick size |

## 4. Forecast comparison

Any claim that "model A beats model B" uses a **Diebold-Mariano test** on OOS loss differentials, not just two IC numbers side by side.

For probability or classification forecasts, check **calibration**: predicted versus realized hit rates by bucket, with Kupiec/Christoffersen-style tests (the VaR backtesting framework from MSQF Risk Management, applied to signal forecasts).

## 5. Economic value (tiered, reported honestly)

1. **Mid-to-mid PnL:** the Lecture 4 method (position proportional to signal, cumulative forward return).
2. **After costs:** spread crossing plus taker fees at a stated fee tier.
3. **After latency:** repeat 2 with decision latency L in {0, 10ms, 100ms, 1s}. Lecture 4 already shows a 1-minute entry delay degrading PnL.
4. Sharpe, drawdown, turnover, number of trades.

A result that dies at step 2 or 3 is still a result: "the effect is statistically real but not harvestable at retail fees and latency" is a legitimate conclusion.

## 6. Pre-registration table (copy into each idea once chosen)

| Item | Value |
|---|---|
| Target variable and horizon(s) | |
| Primary metric | |
| Benchmark it must beat | |
| Significance test | |
| Robustness checks that must pass | |
| What counts as a null result | |
| Holdout period | |
