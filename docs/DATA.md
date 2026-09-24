# Data

**Nothing in this file's scope is ever committed.** The repo is public and the course data is not ours to redistribute. `data/` is gitignored except for its README.

## What we already have (homework dataset)

Binance USD-M futures, **1-minute bars**, 2023-01-01 → 2025-11-30 (2025-11-26/27/28 missing).

| Dataset | Content |
|---|---|
| `level1_1min` | top-of-book stats: open/close/min/max mid, close bid/ask + sizes, spread stats, tick volatility |
| `trade_1min` | OHLC trade prices, buy/sell volume, VWAPs, taker counts |
| `book_1min` | depth snapshot: fill size/VWAP at bps offsets and notional levels |

Symbols: BTCUSDT, ETHUSDT, SOLUSDT, XRPUSDT, DOGEUSDT.

Gotcha: `carryover >= 1` means the bin repeats the previous one (a fake zero return); `carryover == -1` means the fields are NaN. Filter on it before any return or vol statistic.

Use: sanity checks, bar-level baselines, and a cheap first pass on any idea before touching ticks.

## What we request (tick data, available on demand)

The lecture warns "tick data (think twice)", so request narrowly and justify every field. The spec depends on the idea chosen, so fill this in when we commit to one.

| Field | Value |
|---|---|
| Exchange / instrument type | Binance USD-M perpetual futures |
| Symbols | _TBD: idea 01 wants ~12 across sectors; ideas 02/03 can work with the 5 above_ |
| Date range | _TBD: aim for 2-3 months incl. one high-vol stretch and one quiet stretch_ |
| Tables | trades (ts, seq, price, size, side), BBO (ts, seq, bid/ask px + size) |
| L2 depth | _only if an idea needs it (book resiliency, queue effects)_ |
| Extras on demand | funding rates, open interest, liquidations, Hyperliquid trades: _only if used_ |

Other on-demand data listed in the lecture: funding rates, open interest, liquidations, Hyperliquid trades, sentiment.

## Local layout (not committed)

```
data/
├── README.md        <- the only tracked file
├── raw/             <- files exactly as received (read-only, never edited)
│   ├── fordham/     <- homework 1-min archive
│   └── tick/        <- tick delivery
└── hdb/             <- kdb partitioned database built by q/load/
    ├── sym
    └── 2025.10.01/ trade/ quote/ ...
```

## Sharing between the two of us

Data moves outside git: the course delivery link, a shared drive, or an external disk. Each of us builds our own `data/hdb/` locally from `data/raw/` with the loader in `q/load/`, so the HDB is always reproducible from the raw files plus code.

## Data dictionary

_To fill in once the tick files arrive: exact columns, types, timestamp semantics (event vs transaction time), and the anomalies we found._
