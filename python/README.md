# python/ (research layer)

```
python/
├── notset/        # importable package
│   ├── io.py      # PyKX wrappers around q/lib functions
│   ├── features/  # feature construction and transforms
│   ├── models/    # PCA, Kalman, VAR, regressions, classifiers
│   └── eval/      # EVALUATION.md protocol: walk-forward, daily rank IC, DM, bootstrap
├── scripts/       # reproducible entry points, one per result or figure
└── notebooks/     # exploration only; clear outputs before committing
```

Rules
- If a notebook cell gets reused, move it into `notset/`.
- Every figure in `reports/` must be produced by a script in `scripts/`, with the config file and git hash recorded.
- Figures go to `output/` (gitignored). Copy the chosen ones into `reports/figures/`.
