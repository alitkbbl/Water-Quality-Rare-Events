# Water Quality — Rare-Event Estimation

Estimating the probability of a rare water-quality excursion
(`P(Chloramines > τ) ≈ 10⁻⁴`) using **Accept–Reject sampling**, **standard Monte-Carlo**,
and **Importance Sampling**. All core sampling/estimation algorithms are implemented by hand
(only the uniform random primitive is used — no built-in `.rvs()` direct samplers).

## Structure

```
Water_Quality_Rare_Events/
├── data/
│   └── water_potability.csv          # input dataset
├── notebooks/
│   ├── 01_EDA_and_Fitting.ipynb      # EDA + MLE distribution fitting, defines f(x) and τ
│   ├── 02_Accept_Reject_Sampling.ipynb  # Accept–Reject from scratch (Q1)
│   ├── 03_Standard_Monte_Carlo.ipynb    # crude MC estimator, rare-event problem (Q3)
│   └── 04_Importance_Sampling.ipynb     # IS, variance reduction, weights (Q2 & Q4)
├── results/                          # auto-generated plots, CSV tables, fitted_params.json
├── requirements.txt
└── README.md
```

## How to run

```bash
pip install -r requirements.txt
jupyter notebook        # then run notebooks 01 → 02 → 03 → 04 in order
```

Run **Notebook 1 first**: it writes `results/fitted_params.json`, which the others load so the
whole pipeline uses one consistent model. (Notebooks 2–4 also re-fit automatically if that file
is missing, so each can run stand-alone.)

## What each notebook delivers

| Notebook | Content | Analytical question |
|---|---|---|
| 01 | Histograms, summary stats; MLE fits of Normal/Gamma/Weibull compared by AIC/BIC/KS; Q–Q plot; threshold τ for γ ≈ 10⁻⁴ | — |
| 02 | Hand-coded Accept–Reject with a uniform proposal, `M = (b−a)·max f`; histogram match vs real data; acceptance & runtime vs `M` | **Q1** |
| 03 | `γ̂ = (1/N)·Σ I(Xᵢ>τ)` for N = 10³…10⁶; fraction of zero-estimates; log–log error slope ≈ −½ | **Q3** |
| 04 | IS with mean-shifted proposal `q = N(τ,σ)`; ~10³× variance reduction; weight distribution & ESS; lighter/heavier tail study; proposal-shift sweep | **Q2 & Q4** |

## Key results (real data)

- Best fit for Chloramines (near-symmetric): **Normal**, μ ≈ 7.12, σ ≈ 1.58.
- Threshold **τ ≈ 13.01**, true probability **γ ≈ 10⁻⁴**.
- Accept–Reject acceptance ≈ 0.24 ( = 1/M ), KS p-value ≈ 0.93 vs the fitted model.
- Standard MC error decays as N^(−1/2); returns γ̂ = 0 in most runs at N = 10³.
- Importance Sampling: **variance-reduction factor ≈ 2500×** at N = 10⁴.
