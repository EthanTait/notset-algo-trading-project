# Decision log

Newest entries at the top. One entry per decision: what was decided, why, and what was ruled out. If a decision is reversed, add a new entry rather than editing the old one.

---

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
- **Why:** success can't be verified without client or fill data, and it turns the project into a maker-desk simulation built on many assumptions. See `ideas/06-adverse-selection-protective-skew.md`.

---

## Open questions

- [ ] Which idea (or combination) do we commit to for the Oct 7 research plan?
- [ ] Exact tick-data request: symbols, date range, L2 or not
- [ ] Common event grid: fixed interval (100ms / 250ms / 1s) vs refresh-time vs volume clock
- [ ] Maven vs Gradle for `java/`
- [ ] CRO / CTO role split
