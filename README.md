# Water Quality: Rare Event Estimation using Stochastic Simulations

## Overview
This repository contains a comprehensive statistical simulation project focused on water quality analysis. The main objective is to model the distribution of chemical parameters (such as pH or Chloramines) and estimate the probability of extreme, hazardous deviations. Since water treatment systems are generally stable, these deviations are considered "Rare Events" ($P(X > \tau)$).

## Project Objectives
1. **Distribution Fitting:** Identify the underlying probability density function $f(x)$ for the target parameter using real-world data.
2. **Accept-Reject Sampling:** Generate random samples from the target distribution from scratch using an envelope function $g(x)$ and a constant $M$.
3. **Standard Monte Carlo Simulation:** Estimate the probability of the rare event and analyze the variance and error as the sample size $N$ increases.
4. **Importance Sampling:** Implement variance reduction techniques by sampling from a shifted proposal distribution $q(x)$ to efficiently estimate the rare event with a lower computational cost.

## Repository Structure
* `/data`: Place the original dataset (e.g., water quality CSV) here.
* `/notebooks`: Contains the 4 main Jupyter Notebooks for analysis and simulation.
* `/results`: Stores generated plots, histograms, and convergence charts.
* `/report`: Designated folder for the final written documentation.

## Setup
To run the notebooks, ensure you have the following Python packages installed:
`numpy`, `pandas`, `scipy`, `matplotlib`
