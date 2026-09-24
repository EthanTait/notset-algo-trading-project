# java/ (execution layer)

Event-driven replay and execution simulation. Created as a Maven project once the first strategy needs state, latency or an order lifecycle (see [`../docs/ARCHITECTURE.md`](../docs/ARCHITECTURE.md)).

```
java/
├── pom.xml
└── src/
    ├── main/java/notset/
    │   ├── feed/       # EventSource: merged, time-ordered events from q (javakdb) or exported files
    │   ├── engine/     # single-threaded event loop, simulated clock, latency model
    │   ├── exec/       # order + fill models, fees
    │   ├── strategy/   # Strategy interface: onQuote / onTrade / onTimer -> orders
    │   └── report/     # fills + positions written back to q
    └── test/java/notset/
```

Rules
- Deterministic: single-threaded, and any randomness uses a seeded RNG.
- Parameters come from the same `config/*.yaml` that Python uses.
- A strategy's results must reproduce the vectorized Python result when latency = 0 and fills are at mid. That's the first test.
