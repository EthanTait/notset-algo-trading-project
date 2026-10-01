# Signal Research Plan: structure (our summary)

Our own summary of what the course's Signal Research Plan asks for, mapped to where the answers live in this repo. The original handout belongs to the instructors and is **not** committed here.

Due **Oct 7**, submitted **individually** (each of us writes our own, even though the project is shared).

## Motivation (short)

| Asks for | Where we answer it |
|---|---|
| What exactly we predict | `ideas/02` → Target and signal; `ideas/01` → Question |
| Practical use case | `ideas/02` → Which buyer, and why |
| Which market participants could use it | `ideas/02` → buyer table |
| Final deliverable metric for the last slide (PnL / signal-to-noise / information content / execution / risk management / other) | `ideas/02` → Success tiers (execution improvement in bps; IC second) |

## 1. Data

Exchanges and instruments, history length, **sampling frequency and why**, extra data, source. Standard course data is 1-min bars; tick data is on request.
→ `docs/DATA.md`, `ideas/02` → The case for tick data

## 2. Prediction target

Target variable, horizon, false positives to avoid, complicating factors.
→ `ideas/02` → Target and signal, Known traps

## 3. Features

Feature list, a spec for each, expected value ranges, quality and collinearity checks. Don't overdesign.
→ `ideas/01` → Methodology menu (flow, residual returns, betas)

## 4. Optimization and backtest

Parametrization, which parameters are optimized, the optimization metric and procedure, out-of-sample testing, overfit control.
→ `ideas/02` → Methodology menu; `docs/EVALUATION.md`

## 5. Deployment

Real-time data needed, how the signal enters a strategy, the strategy backtest, the final metric.
→ `ideas/02` → The case that we can handle the speed; `docs/ARCHITECTURE.md` → Production path
