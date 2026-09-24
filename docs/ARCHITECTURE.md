# Architecture: q → Python → Java

The stack is fixed; see `DECISIONS.md` (2026-09-24). This file is the contract between the layers, so each of us can work on one layer without breaking the others. What goes *through* the pipeline depends on the idea we pick.

```
 raw ticks (.csv.gz from course)
        │  named pipe + .Q.fps (streaming load, never fully in memory)
        ▼
┌───────────────────────────────┐
│  LAYER 1 · kdb+/q             │   storage + cleaning + event-time joins
│  HDB: trade, quote, (depth)   │   q/lib/*.q  = the only way research touches ticks
│  derived: grids, features     │
└──────────────┬────────────────┘
               │ PyKX (embedded or IPC)
               ▼
┌───────────────────────────────┐
│  LAYER 2 · Python             │   features, models, evaluation
│  python/notset/               │   writes signals + params back to q / config
└──────────────┬────────────────┘
               │ event stream + signals, pulled from q via javakdb (IPC)
               ▼
┌───────────────────────────────┐
│  LAYER 3 · Java               │   event-driven replay engine
│  java/                        │   latency + fill models, strategy state
└──────────────┬────────────────┘
               │ fills, positions  → written back to q → evaluated in Python
               ▼
            results
```

## Layer 1: kdb+/q (data)

**Responsibilities**
- Ingest raw course files into a **date-partitioned HDB** (`data/hdb/`), sorted by `sym, time` with the `p#` attribute on `sym`.
- Clean: remove duplicates by `seq`, flag crossed or locked books, flag gaps, keep exchange timestamps as they are.
- Provide a **query library** (`q/lib/`). Research code calls these functions and never writes ad-hoc joins against raw tables. Candidates:
  - `bars[tbl; syms; d0; d1; bucket]`: clock-time bars via `xbar`
  - `grid[syms; d0; d1; mode]`: common event grid (fixed-interval, refresh-time, or volume clock)
  - `markout[events; horizons]`: forward mids via `aj`
  - `flow[syms; window]`: signed volume / imbalance via `wj`
- Store heavy derived tables (grids, feature panels) back into the HDB as their own partitioned tables so Python loads them instead of recomputing.

**Core schemas** (from the Lecture 3/4 data spec; adjust once the actual tick files arrive)

```q
trade:([] date:`date$(); sym:`symbol$(); time:`timestamp$(); seq:`long$();
          price:`float$(); size:`float$(); side:`short$())          / side: 1 buy-aggressor, -1 sell
quote:([] date:`date$(); sym:`symbol$(); time:`timestamp$(); seq:`long$();
          bid:`float$(); bsize:`float$(); ask:`float$(); asize:`float$())
/ optional, only if an idea needs it:
depth:([] date:`date$(); sym:`symbol$(); time:`timestamp$(); seq:`long$();
          isbid:`boolean$(); px:`float$(); qty:`float$())            / L2 incremental updates
```

**Conventions**
- `time` is a q `timestamp` (ns) in **UTC**, converted from exchange epoch-ms. We never store local time.
- Within a timestamp, order events by `seq`. Trades sharing `time` and `side` usually belong to one taker order sweeping levels. Keep them as separate rows and aggregate in queries.
- Community Edition limits (as documented at time of writing): about 16 GB RAM per process, 4 secondary threads, 8 IPC connections. A partitioned HDB memory-maps from disk, so the RAM cap limits how much a *single query* can hold, not the dataset size. Query one date or symbol at a time.

## Layer 2: Python (research)

**Responsibilities**: feature construction beyond what's easier in q, model fitting (regression, PCA, Kalman, VAR, classifiers), the evaluation protocol from `EVALUATION.md`, and plots.

**Interface to q**
```python
import pykx as kx
# embedded (q runs inside the Python process; needs licence via QLIC)
kx.q('\\l data/hdb')
df = kx.q('grid[`BTCUSDT`ETHUSDT; 2025.10.01; 2025.10.07; `refresh]').pd()
# or IPC to a running q process (counts against the 8-connection limit)
with kx.SyncQConnection(port=5010) as q:
    df = q('markout', events, horizons).pd()
```

**Structure**
```
python/
├── notset/          # importable package: io.py (PyKX wrappers), features/, models/, eval/
├── scripts/         # reproducible entry points, one per result/figure
└── notebooks/       # exploration only; outputs stripped before commit
```

## Layer 3: Java (execution)

**When it earns its place**: vectorized mid-to-mid PnL (the Lecture 4 method) runs in Python/q. Java takes over once a strategy needs **sequential state**, which is awkward in vectorized q and slow in Python loops:
- decision latency and order latency (signal at t, order arrives at t + L)
- position and risk limits, cooldowns, kill switches
- order lifecycle (send / ack / fill / cancel) and fill models (taker at touch + fee; passive with a queue model if we ever need it)
- deterministic tick-by-tick replay of a merged `trade` + `quote` event stream

**Sketch**
```
java/
├── pom.xml
└── src/main/java/notset/
    ├── feed/        # EventSource: pulls merged, time-ordered events from q (javakdb) or exported files
    ├── engine/      # single-threaded event loop, simulated clock, latency model
    ├── exec/        # order/fill models, fees
    ├── strategy/    # Strategy interface: onQuote, onTrade, onTimer -> orders
    └── report/      # writes fills + positions back to q for evaluation in Python
```

**Rules**: single-threaded and deterministic (a seeded RNG if anything is random), and the same parameters from `config/` that Python uses. Signals computed in Python are either passed in as a precomputed column in the event stream (research mode) or re-implemented in Java incrementally (production mode). The gap between those two modes is itself a measurable result: Lecture 4 says production features differ from research ones.

## Config

One YAML file per experiment in `config/`, read by all three layers (q via a small loader, Python via `pyyaml`, Java via SnakeYAML). It holds symbols, dates, horizons, windows, latencies and fees. Results record the config file name and git commit hash they came from.
