# Phase 1 — Gold & Silver Price Research

**Scope of this phase:** build a verified 100-year gold and silver dataset, and a baseline
next-month forecasting model with honest, walk-forward validation. Everything later phases
do will be measured against this baseline.

## What's here

| File | What it is |
|---|---|
| `phase1_gold_silver_100y.ipynb` | The whole phase: downloads data, builds the dataset, trains and scores the baseline. Run top-to-bottom. |
| `data/gold_silver_100y_annual.csv` | Annual gold & silver, 1926 → today, with era labels (created when the notebook runs) |
| `data/gold_silver_monthly_1968.csv` | Monthly LBMA auction averages, 1968 → today (created when the notebook runs) |
| `data/phase1_walkforward_predictions.csv` | Out-of-sample predictions vs actuals (created when the notebook runs) |

## Data sources — stated, not implied

- **1968 → today:** LBMA official auction prices (gold PM, silver), free public JSON feed. Personal research use.
- **1926–1967 gold:** the official US price — exact by definition ($20.67, then $35.00 after the 1934 revaluation).
- **1926–1967 silver:** approximate annual averages from decade anchors, flagged `approx_silver = True`,
  used for charts only and **never for model training**. Replace with the USGS Historical Statistics
  series if exact values are ever needed.
- Fallback if LBMA is unreachable: Stooq spot series (XAUUSD / XAGUSD). The notebook prints which
  source it actually used on every run.

## Design decisions worth knowing

1. **Spot prices, not ETFs.** The original fork used the GLD ETF, which has only existed since 2004 —
   a 100-year history of it is impossible.
2. **The model trains on 1971 → today only.** Before 1971 the gold price was fixed by government.
   Fixed prices contain no signal; the earlier data is kept for context, not training.
3. **No look-ahead.** The old approach predicted same-day price from same-day inputs, which is
   circular. Here every feature is lagged and validation is walk-forward: train on the past,
   predict the next year, roll forward.
4. **A naive benchmark is always shown.** If the model can't beat "predict no change,"
   the notebook says so instead of hiding it.

## How to run

Easiest: open [colab.google](https://colab.google) → **GitHub** tab → paste this repo's URL →
open `phase1_gold_silver_100y.ipynb` → Runtime → Run all. No keys, no setup.

Locally: `pip install pandas scikit-learn matplotlib requests`, then open the notebook in Jupyter.

## Roadmap

- **Phase 1 (this repo):** data foundation + measured baseline. ✅
- **Phase 2:** macro features (real interest rates, dollar index, CPI — all free from FRED),
  regime awareness, probability bands instead of point forecasts.
- **Phase 3:** integration with the live analysis dashboard.
