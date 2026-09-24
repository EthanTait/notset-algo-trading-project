# notset-algo-trading-project

Group project for **Blockchain, Cryptocurrency and Algorithmic Trading** (Fordham MSQF, Fall 2026).

Two people, tick data, and a search for short-horizon alpha that may well not exist. The aim is to run that search rigorously enough that a null result still means something.

> **Status: ideation.** The stack is fixed; the research question is not. Candidate ideas live in [`ideas/`](ideas/).

---

## What is fixed

| Decision | Detail |
|---|---|
| **Data layer: kdb+/q** | Tick storage (date-partitioned HDB), cleaning, event-time joins, and every query research depends on |
| **Research layer: Python** | Features, models, evaluation, plots. Talks to q through PyKX |
| **Execution layer: Java** | Event-driven replay and execution simulation for strategies that need state, latency or order lifecycle. Talks to q through javakdb |
| **Tick data** | Requested from the course (Binance USD-M futures). The 1-min bar data from the homework is the fallback and sanity check |
| **Evaluation protocol** | Shared across all ideas and fixed *before* results. See [`docs/EVALUATION.md`](docs/EVALUATION.md) |

The q → Python → Java split mirrors a production trading-desk stack. The layers and their interfaces are specified in [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md).

## What is open

The thesis. Each file in [`ideas/`](ideas/) is a candidate with its own question, methodology menu and success tiers. They are written as options, not plans, so we can combine, narrow or drop them as the data comes in. The comparison table is in [`ideas/README.md`](ideas/README.md).

## Repository layout

```
notset-algo-trading-project/
├── README.md            <- you are here
├── CONTRIBUTING.md      <- git workflow, commit rules, what never gets committed
├── .gitignore
├── .gitattributes       <- forces LF line endings (Windows + WSL team)
├── docs/
│   ├── ARCHITECTURE.md  <- q -> Python -> Java pipeline, schemas, interfaces
│   ├── SETUP.md         <- WSL2, KDB-X Community, PyKX, JDK, VS Code
│   ├── DATA.md          <- what data we have, what we request, how it is stored
│   ├── EVALUATION.md    <- shared success measures and anti-overfitting rules
│   └── DECISIONS.md     <- dated decision log
├── ideas/               <- one markdown per candidate thesis
├── q/                   <- kdb+/q: schemas, loaders, HDB build, query library
├── python/              <- research package, scripts, notebooks
├── java/                <- replay / execution engine (Maven project)
├── config/              <- shared YAML parameters read by all three layers
├── reports/             <- research plan, milestones, paper, slides
└── data/                <- LOCAL ONLY, gitignored (raw files + HDB)
```

## Course deliverables (check dates against the syllabus)

| Deliverable | Type | Due |
|---|---|---|
| Research Plan | Individual | Oct 7 |
| Group Milestone 1 | Group | TBD |
| Group Milestone 2 | Group | TBD |
| Presentation slides + presentation | Group | TBD |
| Research paper | Group | TBD |

The Research Plan template (from the Lecture 4 group-projects slides) asks for: exact data spec, a precise target and horizon, false positives to avoid, and a feature spec with expected value ranges and a collinearity check. Each idea file is organized so it can be dropped into that template.

## Team

| Role (course definition) | Scope | Who |
|---|---|---|
| CRO | Data cleaning, models, optimization, backtest | TBD |
| CTO | GitHub, development, data acquisition/formatting, production | TBD |

Members: Ethan Tait, `<teammate>`.

## Getting started

1. Set up the environment: [`docs/SETUP.md`](docs/SETUP.md)
2. Read the pipeline contract: [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md)
3. Pick or propose an idea: [`ideas/README.md`](ideas/README.md)
