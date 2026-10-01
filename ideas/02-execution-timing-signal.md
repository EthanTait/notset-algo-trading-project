# 02 · Execution-timing signal (pitched to an execution desk)

**Status:** candidate. This is the *application* of idea 01: 01 builds the signal, 02 is who uses it and how we measure that.
**One line:** use the short-horizon drift forecast from 01 to time an execution desk's child orders, and measure the slippage saved against a plain schedule on simulated parent orders.

---

## How we got here (context)

- The **Signal Research Plan** (due Oct 7, individual) asks for a motivation section: what we predict, the practical use case, **which market participants would use it**, and a **final deliverable metric** for the last slide. The listed options include PnL improvement, signal-to-noise improvement, information content improvement, **execution improvement** and risk-management improvement. A summary is in `docs/RESEARCH_PLAN_TEMPLATE.md`.
- Our framing: **pitch the signal to a buyer**, such as a bank execution desk, a hedge fund or a market maker. The execution desk is the best fit. That also lets us **tone the scope down**, as long as we make a strong case for **why tick data** and **how we handle its speed**.
- Constraints carried over from ideation (see `docs/DECISIONS.md`):
  - No maker / RFQ-dealer framing: success can't be verified without client data.
  - Avoid the event trap: success measures are fixed in advance and per-day (`docs/EVALUATION.md`).
  - Fixed stack: q → Python → Java.

## Which buyer, and why

| Buyer | What the signal must do | Fit |
|---|---|---|
| **Execution desk** (e.g. a bank's agency or digital-asset desk, a prime broker, or a fund's own execution team) | Improve the **timing** of child orders it's sending anyway | **Best.** No standalone alpha needed |
| Hedge fund (stat-arb) | Make money on its own after spread + fees | Hard. Taker fees likely eat a short-horizon signal |
| Market maker | Skew quotes against adverse selection | Parked: needs fill / client data to verify |

**The pitch in one sentence:** an execution desk pays the spread and fees whether or not it uses our signal, so the signal only has to decide "this slice now, or a few seconds later?" better than a fixed schedule. A signal far too weak to trade on its own can still save measurable bps on large parent orders.

## Why this is verifiable with market data only

There's no counterparty to invent. We act as the desk, working our own simulated orders:

1. Generate many **synthetic parent orders**, e.g. "buy X notional of SOL over T minutes", with **randomized start times and randomized side** (buy or sell), so market drift cancels and no single event dominates.
2. Execute each parent order two ways:
   - **Baseline:** equal child orders every N seconds (TWAP-style schedule).
   - **Signal-aware:** same total quantity and same deadline, but slices can be pulled forward or delayed within limits based on the signal.
3. Fill each child order as a **taker at the observed touch + fee**, keeping child size at or below the displayed touch size so no L2 book is needed.
4. Compare execution cost per parent order:

```
IS_bps      = side * (avg_fill_px - arrival_mid) / arrival_mid * 1e4  + fee_bps
improvement = IS_baseline - IS_signal          (positive = signal helped)
```

The result is reported as a **distribution across parent orders and across days**, not as one pooled number.

## Target and signal

- **Target:** drift of the *asset being executed* over the next h, where h is matched to the slice interval (seconds to tens of seconds).
- **Signal:** idea 01's forecast. Predicted drift is the market part (BTC-led) plus the idiosyncratic part (from the current leader, via the market/idiosyncratic split of price and flow).
- **Validation:** 01's market-neutral test is not the product. It's the evidence that the signal carries information beyond beta, which makes a strong slide.

## Methodology menu

| Step | Options |
|---|---|
| Parent orders | notional sizes × horizons × symbols; random start times; random side |
| Benchmark | arrival price (implementation shortfall) · baseline schedule (TWAP) · interval VWAP. Report several; see real-world input below |
| Baseline schedule | equal slices every N seconds |
| How the signal is used | **tilt** the schedule (pull forward / delay within a backlog limit, same deadline) · **pause** slices when price is forecast to come to us · passive-vs-aggressive choice (stretch only; needs a queue model) |
| Fill model | taker at touch + fee; child size ≤ touch size; no market impact in the core run, a simple impact add-on as a sensitivity check |
| Latency | the signal uses data up to decision time minus latency L; sweep L |
| Optimized parameters | tilt threshold, max backlog, horizon h, fitted on sliding IS/OOS windows (Lecture 5 parameter-stability slides; Quant Methods IS/OOS rules) |

## Real-world input and assumptions

| Item | Status | How we handle it |
|---|---|---|
| Slippage benchmark | **Ethan:** client-dependent in practice, no single standard | Report arrival-price IS (primary), TWAP and interval VWAP side by side |
| How an algo consumes a short-horizon signal | **Ethan:** not observed directly | Our design choice, stated as such; start with schedule tilt, the others in the menu |
| Parent horizon / slice cadence | **Unknown** | Sweep a grid of horizons and cadences and flag it as an assumption, so no result depends on one choice |
| Where the live signal is computed | **Ethan:** data arrives and is processed in q, so the signal is computed in q in the same pass and pushed to the Java engine. The long part is market-data latency; computing the signal is quick by comparison | Production design in `docs/ARCHITECTURE.md` follows this; the latency budget is split into market-data latency vs compute time |
| Taker / maker fees | To verify | Use the current Binance USD-M fee schedule at a stated tier; sensitivity table |

## The case for tick data

1. **The information sits inside the 1-min bar.** First figure: BTC → alt cross-correlation on the homework 1-min data. If it's concentrated at lag 0 (same bar), the lead is shorter than a bar and bars can't see it.
2. **Execution needs the touch when each slice is sent.** 1-min bars only carry the close bid/ask, so a child order sent 10 seconds into a minute can't be priced.
3. **Asynchronous trading** biases lead-lag measured at fine resolution (the Epps effect) unless you have real timestamps.

## The case that we can handle the speed

Research speed and production speed are separate problems:

- **Research is throughput, not latency.** The q HDB is partitioned by date, stored by column and memory-mapped, so we query one day at a time. `aj` joins are vectorized, and derived grids are stored once and reused. We report dataset size and build time.
- **Production is a latency budget set by the signal itself.** The research produces the signal's **decay curve**. If information decays over seconds, a server reading the exchange's public websocket streams (trades, best bid/offer) is enough and colocation isn't needed. The argument is "measured half-life >> pipeline latency."
- **Where the latency actually is.** Data is received and processed in q, and the signal is computed in the same pass, so the budget splits into:

  ```
  total latency = market-data latency (exchange -> us)  +  q compute (signal)  +  push to Java  +  order send
                  ^ the long part                          ^ small by comparison
  ```

  We measure the q compute time per update directly (e.g. `\t` timings over replayed events) to show it's small next to the market-data leg.
- **Demonstration:** replay historical events through the pipeline (q real-time process → Java engine) and report **events processed per second vs the peak event rate in our data**.

## Pipeline mapping

- **q:** HDB (trades + BBO), event grids, signal features. In production, the same q process that receives and processes the data computes the signal in that pass and pushes it to Java.
- **Python:** signal fitting (from 01), parameter optimization on sliding windows, evaluation and plots.
- **Java:** the execution simulator: slice schedule, backlog/carryover, deadline enforcement, latency, fills, fees. All of it is stateful, which is why it lives in Java.

## Success tiers

1. **Descriptive:** lag-0 figure on 1-min data (the motivation); signal decay curve on ticks.
2. **Predictive:** OOS daily rank IC of the drift forecast (`docs/EVALUATION.md`).
3. **Economic, and the final deliverable:** **execution improvement in bps vs benchmark**, as a per-day distribution with bootstrap confidence intervals, plus the latency at which the improvement disappears.
4. **Engineering:** events-per-second throughput against the peak market rate.

## Known traps

- **Drift / side bias.** Randomize side and start time, or a trending sample flatters one side.
- **Child size vs displayed depth.** Cap child size at touch size, or the fill price is fiction.
- **Look-ahead.** The signal at decision time may only use data up to decision time minus latency.
- **Overlapping parent orders** share market moves, so bootstrap by day, not by order.
- **Fees vs improvement.** Fees are paid in both arms, so they cancel in the *difference*, but report gross and net anyway.
- **Event trap.** Use per-day metrics and drop the top 5 vol days as a robustness check.

## Data needs

- Binance USD-M **trades + BBO** for the 5 homework symbols (BTC, ETH, SOL, XRP, DOGE), plus HYPE if available (it's on the course's liquid list). 1-2 months. No L2.

## Scope ladder

- **Minimum viable:** BTC → one alt; BTC-only drift signal; TWAP vs tilt; taker fills; vectorized simulation in Python.
- **Extension:** 01's market/idiosyncratic signal; Java simulator with latency; several benchmarks; full symbol set.
- **Stretch:** passive-vs-aggressive slicing (needs a queue model / L2); real-time replay through q (process + signal in one pass) → Java, with throughput and compute-time benchmarks.

## Links to lectures and coursework

- Lecture 4: features, cross-correlation, mid-to-mid backtest, the 1-minute-delay slide, the realizability gotchas.
- Lecture 5: Research Plan structure, sliding-window optimization, parameter stability.
- MSQF: IS/OOS discipline and factor models (Quant Methods); PCA / Kalman for the decomposition (Time Series Econometrics); bootstrap and calibration ideas from VaR backtesting (Risk Management).

## Open questions

- Which benchmark goes first on the final slide (arrival IS seems most natural)?
- What parent sizes are realistic relative to displayed touch size for the alts?
- Is HYPE in the tick dataset?
- Do we keep the passive-vs-aggressive choice out of scope entirely?
