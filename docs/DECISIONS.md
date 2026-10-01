# Decision log

Newest entries at the top. One entry per decision: what was decided, why, and what was ruled out. If a decision is reversed, add a new entry rather than editing the old one.

---

### 2026-10-01: Live signal computed in q, in the same pass as data processing
- **Decided:** in production, data is received and processed in q and the signal is computed in that same pass, then pushed to the Java engine.
- **Why (Ethan, from a q → Python → Java desk stack):** the long part is market-data latency; computing the signal is quick by comparison. We measure q compute time per update to back this up.

### 2026-10-01: Benchmarks reported side by side
- **Decided:** report execution improvement against arrival price (implementation shortfall, primary), the baseline TWAP schedule, and interval VWAP.
- **Why (Ethan):** in practice the benchmark is client-dependent; there's no single standard.

### 2026-10-01: Pitch the signal to an execution desk; ideas narrowed to 01 + 02
- **Decided:** keep `ideas/01` (cross-asset leadership, the signal engine) and add `ideas/02` (execution-timing application). Ideas 02-06 from 2026-09-24 removed (still in git history), including the parked adverse-selection file.
- **Why:** the Signal Research Plan asks for a use case, a buyer and a final deliverable metric. An execution desk is the best-fit buyer: it pays spread and fees anyway, so the signal only has to improve timing, and success (slippage saved vs benchmark on simulated parent orders) is verifiable with market data alone, without client data.
- **Also:** scope toned down: the 5 homework symbols (+HYPE if available), trades + BBO, no L2, taker-only fills in the core.
- **Not observed in practice (Ethan):** exactly how an execution algo consumes a short-horizon signal. Schedule tilt is our design choice and is stated as such.

### 2026-09-24: Stack fixed: kdb+/q → Python → Java
- **Decided:** q for tick storage and querying, Python for research, Java for event-driven execution simulation.
- **Why:** tick data needs event-time joins at scale, which is q's strength. The split mirrors a production trading-desk stack, which is part of what we want to learn.
- **Condition on Java:** it enters once a strategy needs state, latency or an order lifecycle. Vectorized mid-to-mid tests stay in Python/q.
- **Licensing:** KDB-X Community Edition (free, per-person licence), OpenJDK 21, conda Python.

### 2026-09-24: Repo is public
- **Consequence:** no data, no licence files, no credentials in git, ever (see `CONTRIBUTING.md`).

### 2026-09-24: Evaluation protocol before thesis
- **Decided:** success metrics are shared across ideas and fixed before looking at OOS results (`EVALUATION.md`).
- **Why:** avoids the event trap and stops the thesis drifting toward whatever the data shows.

### 2026-09-24: Adverse-selection / protective-skew framing parked
- **Why:** success can't be verified without client or fill data, and it turns the project into a maker-desk simulation built on many assumptions. See `ideas/06-adverse-selection-protective-skew.md` (removed 2026-10-01, still in git history).

---

## Open questions

- [ ] Exact tick-data request: date range; is HYPE available?
- [ ] Common event grid: fixed interval (100ms / 250ms / 1s) vs refresh-time
- [ ] Parent-order grid (sizes, horizons, slice cadence), flagged as assumptions
- [ ] Which benchmark leads the final slide
- [ ] Maven vs Gradle for `java/`
- [ ] CRO / CTO / COO role split, group name
