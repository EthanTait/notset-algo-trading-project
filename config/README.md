# config/

One YAML file per experiment, read by q, Python and Java

Example shape (fields will change with the chosen idea):

```yaml
name: leadership_v0
symbols: [BTCUSDT, ETHUSDT, SOLUSDT, XRPUSDT, DOGEUSDT]
dates: {start: 2025-10-01, end: 2025-10-31, holdout_start: 2025-10-25}
grid: {mode: fixed, interval_ms: 250}
windows: {estimation: 1h, step: 15m}
horizons_ms: [250, 1000, 5000, 30000]
latency_ms: [0, 10, 100, 1000]
fees_bps: {taker: 5.0, maker: 2.0}
paths: {hdb: data/hdb, output: output}
```

Results record the config file name and git commit hash that produced them.
