# notset-algo-trading-project

Group project for **Blockchain, Cryptocurrency and Algorithmic Trading** (Fordham MSQF, Fall 2026).

Two people, tick data, and a search for short-horizon alpha that may well not exist. The aim is to run that search rigorously enough that a null result still means something.

> **Status: research plan.** The stack is fixed. The working direction is a cross-asset leadership signal ([`ideas/01`](ideas/01-cross-asset-leadership.md)), pitched to an execution desk as a way to time child orders ([`ideas/02`](ideas/02-execution-timing-signal.md)).

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

Methodology details. The two idea files in [`ideas/`](ideas/) are written as menus of methods and success tiers, not fixed plans: **01** is the signal, **02** is who uses it and how we measure that. How the two connect is in [`ideas/README.md`](ideas/README.md).

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
│   ├── RESEARCH_PLAN_TEMPLATE.md <- what the research plan asks, mapped to our docs
│   └── DECISIONS.md     <- dated decision log
├── ideas/               <- 01 signal engine, 02 execution-desk application
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
| Group formation (name, title, roles, summary) | Group | Next class (slide says "Wed Oct 8"; confirm) |
| Group Milestone 1 | Group | TBD |
| Group Milestone 2 | Group | TBD |
| Presentation slides + presentation | Group | TBD |
| Research paper | Group | TBD |

What the Research Plan asks for, and where each answer lives in this repo, is in [`docs/RESEARCH_PLAN_TEMPLATE.md`](docs/RESEARCH_PLAN_TEMPLATE.md).

## Team

| Role (course definition) | Scope | Who |
|---|---|---|
| CRO | Data cleaning, models, optimization, backtest | TBD |
| CTO | GitHub, development, data acquisition/formatting, production | TBD |
| COO | Presentation, paper, organization, logistics | TBD |

Members: Ethan Tait, `<teammate>`.

## Getting started

1. Set up the environment: [`docs/SETUP.md`](docs/SETUP.md)
2. Read the pipeline contract: [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md)
3. Read the two ideas: [`ideas/README.md`](ideas/README.md)
