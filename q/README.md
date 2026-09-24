# q/ (data layer)

kdb+/q code: schemas, loaders, HDB build, and the query library research depends on. The contract is in [`../docs/ARCHITECTURE.md`](../docs/ARCHITECTURE.md).

```
q/
├── schema/    # table definitions (trade, quote, depth, derived tables)
├── load/      # raw .csv.gz -> partitioned HDB (named pipe + .Q.fps, .Q.dpft)
├── lib/       # reusable query functions: bars, grid, markout, flow, ...
└── init.q     # loads schema + lib, mounts data/hdb; entry point for PyKX and the IDE
```

Rules
- Research code calls functions in `lib/`, not ad-hoc queries against raw tables.
- Paths come from `config/`, never hardcoded.
- Never write into `data/raw/`.
