# 🧪 Water Quality Rare-Event Estimation

A computer simulation project that models a real-world water-quality dataset and **estimates the probability of a rare contamination event (Chloramine concentration exceeding a critical threshold)** using classical and variance-reduction Monte Carlo techniques — with all core sampling algorithms implemented from scratch rather than using built‑in random‑variate generators.

---

## 📑 Project Documentation

Two full project reports (LaTeX-typeset, with all derivations, tables, and figures) are included in this repository:

- 📄 **[Report — English (EN)](report/report_en.pdf)**
- 📄 **[Report — Persian (FA)](report/report_fa.pdf)**


Each report walks through the full methodology, mathematical justification, and analytical answers to the project's required questions, including:
- The effect of the covering constant $M$ on acceptance rate and runtime.
- Tail-thickness analysis and weight stability via Effective Sample Size (ESS).
- Empirical verification of the $O(N^{-1/2})$ Monte Carlo convergence rate.
- Optimization of the Importance Sampling shift parameter $\mu_q$ and the failure modes of a poorly chosen proposal.

  
---

## 📌 Overview

Water treatment systems are designed to be highly stable, which means that a chemical parameter (such as pH or Chloramines) crossing its safety threshold is, statistically speaking, a **rare event**: something with very low probability that is hard to observe with limited or naive sampling.

This project builds a complete statistical and computational pipeline to study exactly that problem:

1. **Model the real data** — fit a probability distribution to the `water_potability.csv` dataset (Chloramines was selected as the target variable, since it has no missing values and represents a genuine health-risk parameter).
2. **Generate synthetic data manually** — implement the **Accept–Reject** algorithm from first principles to sample from the fitted distribution, since ready-made random generators (e.g. `scipy.stats.norm.rvs`) are not allowed.
3. **Quantify the rare event** — define a critical threshold $\tau$ corresponding to a true exceedance probability of $\gamma = 10^{-4}$.
4. **Estimate the probability** — first with **Standard (Crude) Monte Carlo**, exposing its weaknesses (zero-estimate phenomenon, high relative error, slow $O(N^{-1/2})$ convergence), and then with **Importance Sampling**, which shifts the sampling distribution toward the critical region to drastically reduce variance.

The result is a full comparison of how a "boring" naive simulation breaks down on rare events, and how a well-designed importance distribution fixes it — backed by acceptance-rate analysis, convergence plots, importance-weight diagnostics (ESS), and a sensitivity study of the proposal shift parameter.

---

## 📂 Repository Structure

```
Water_Quality_Rare_Events/
├── data/
│   └── water_potability.csv          # input dataset
├── notebooks/
│   ├── 01_EDA_and_Fitting.ipynb         # EDA + MLE distribution fitting, defines f(x) and τ
│   ├── 02_Accept_Reject_Sampling.ipynb  # Accept–Reject from scratch (Q1)
│   ├── 03_Standard_Monte_Carlo.ipynb    # crude MC estimator, rare-event problem (Q3)
│   └── 04_Importance_Sampling.ipynb     # IS, variance reduction, weights (Q2 & Q4)
├── results/                          # auto-generated plots, CSV tables, fitted_params.json
├── report/                             # compiled FA/EN project reports (PDF)
├── requirements.txt
└── README.md
```

---

## 🔬 Methodology

The project progresses through four sequential phases, each building on the previous one:

### 1️⃣ Exploratory Data Analysis & Distribution Fitting
The Chloramines column is analyzed (mean ≈ 7.12, std ≈ 1.58, skewness ≈ 0, i.e. nearly symmetric). Three candidate distributions — **Normal**, **Gamma**, and **Weibull** — are fit via a manually implemented **Maximum Likelihood Estimation (MLE)** routine and cross-validated against `scipy`. Model selection uses log-likelihood, AIC, BIC, and the Kolmogorov–Smirnov statistic. The **Normal distribution** ($\mu = 7.1223$, $\sigma = 1.5828$) is selected as the best-fitting model, and the rare-event threshold is set at **$\tau \approx 13.0089$**, corresponding to a true exceedance probability of **$\gamma = 10^{-4}$**.

### 2️⃣ Accept–Reject Sampling
Using the fitted Normal as the target $f(x)$ and a Uniform proposal $g(x)$ on $[0, \mu + 6\sigma]$, the covering constant $M$ is derived analytically ($M \approx 4.19$), and 100,000 synthetic samples are generated through a manual Accept–Reject loop. Validity is confirmed via moment matching, a KS test ($p \approx 0.93$), and visual histogram comparison. A sweep over proposal widths quantifies the $1/M$ acceptance-rate law and its $O(M)$ runtime cost — motivating the need for smarter sampling at the tail.

### 3️⃣ Standard Monte Carlo
The crude estimator $\hat{\gamma} = \frac{1}{N}\sum_{i=1}^{N} \mathbb{1}(X_i > \tau)$ is evaluated at $N = 10^3, 10^4, 10^5, 10^6$. The results expose the **zero-estimate phenomenon** (the estimator returns exactly zero in the majority of trials at low $N$) and a relative error that scales as $1/\sqrt{N\gamma}$ — empirically confirming the textbook $O(N^{-1/2})$ convergence rate on a log-log error plot.

### 4️⃣ Importance Sampling
A proposal distribution $q(x) = \mathcal{N}(\mu_q = \tau,\ \sigma_q = \sigma)$ is constructed to concentrate sampling near the rare-event region, with each sample reweighted by $w(x) = f(x)/q(x)$ to preserve unbiasedness. This reduces estimator variance by a factor of roughly **2,300–3,200×** relative to crude MC, eliminating the zero-estimate problem entirely and shrinking the relative error from ~103% to ~2% at $N = 10^4$. The project further studies:
- **Tail thickness vs. weight stability** — using Effective Sample Size (ESS) to show why the proposal must have a tail at least as heavy as the target.
- **Proposal shift optimization** — a U-shaped relative-error curve over $\mu_q$, identifying the sweet spot near the threshold and showing how both under-shifting and over-shifting degrade performance.

---


## 🚀 Getting Started

```bash
git clone https://github.com/alitkbbl/Water-Quality-Rare-Events.git
cd Water-Quality-Rare-Events
pip install -r requirements.txt
```

Run the notebooks in order (`01` → `04`) inside `notebooks/`; each one saves its figures and tables into `results/`.

---

## ✅ Conclusion

This project demonstrates, end to end, why naive Monte Carlo simulation is fundamentally unsuited to rare-event estimation, and how **Importance Sampling** — through a deliberate, mathematically justified change of measure — turns an intractable problem into a fast, stable, and statistically sound estimation task. Every random-sampling component (Accept–Reject, crude MC, importance proposal) is implemented from first principles, making the codebase a self-contained reference for variance-reduction techniques in reliability and risk-analysis contexts.
