# Heston-Stochastic-Volatility-Model-Simulation-Pricing-Implied-Volatility
Heston stochastic volatility in Python: Euler simulation, Heston vs Black-Scholes pricing, implied volatility smile, sensitivity to ρ and ξ, Feller condition and Fourier pricing via the characteristic function. Link: https://colab.research.google.com/drive/1WGFlJEZ4Y5sqm7K6NS8PaixJON_CxEKx?usp=sharing


## Overview

This project implements the Heston stochastic volatility model in Python and studies how it differs from Black-Scholes. It is Homework 8 of the A.Y. 2025-2026 coursework. The model combines a price process with a mean-reverting variance process, linked by a negative correlation (ρ = −0.7) that reproduces the leverage effect seen in equity markets.

### Model parameters

| Parameter | Symbol | Value |
|---|---|---|
| Mean reversion speed | κ | 2.0 |
| Long-run variance | θ | 0.04 (20% vol) |
| Vol of vol | ξ | 0.5 |
| Correlation | ρ | −0.7 |
| Initial variance | v₀ | 0.04 |
| Spot | S₀ | 100 |
| Drift | μ | 0.02 |
| Maturity | T | 1 year |
| Time steps / paths | N / M | 250 / 10,000 |

### Simulation

Price and variance paths are generated with an Euler-Maruyama scheme, using correlated Brownian increments drawn from a bivariate normal. A reflection scheme keeps the variance usable inside the square root. The paths show the leverage effect: downside paths are steeper than upside paths, and variance spikes when the price falls.

### Heston vs Black-Scholes: terminal distribution

The terminal distribution is compared with a constant-volatility benchmark (σ = √θ = 20%). The means match, but Heston has negative skewness while Black-Scholes has positive skewness, a direct effect of ρ < 0.

| Statistic | Black-Scholes | Heston |
|---|---|---|
| Mean | 101.84 | 102.00 |
| Variance | 417.07 | 355.10 |
| Skewness | 0.637 | −0.621 |
| Kurtosis | 0.875 | 0.792 |

### European call pricing

Calls are priced by Monte Carlo and compared with the Black-Scholes closed form. The difference moves from positive to negative as the strike rises, which is the fingerprint of a negative volatility skew.

| Strike K | BS price | Heston price | Difference |
|---|---|---|---|
| 90 | 14.81 | 15.05 | +0.25 |
| 100 | 8.92 | 8.33 | −0.59 |
| 110 | 4.94 | 3.57 | −1.37 |

### Implied volatility smile

Inverting the Heston prices with Brent's method (`scipy.optimize.brentq`) gives a downward-sloping skew, from about 26% at K = 70 to about 14% at K = 130. Varying ρ tilts the smile, while varying ξ changes its steepness and curvature.

| Case | Effect on the smile |
|---|---|
| ρ = −0.9 | Steep skew, from about 27.5% (K = 70) to below 11% (K = 130) |
| ρ = −0.5 | Moderate skew |
| ρ = 0 | Mild symmetric smile |
| ξ = 0.2 | Almost flat, close to Black-Scholes |
| ξ = 0.5 | Moderate skew |
| ξ = 1.0 | Steep and asymmetric, about 28% at low strikes |

### Feller condition and variance discretisation

The Feller condition (2κθ ≥ ξ²) is violated with these parameters. Euler pure, full truncation and reflection are compared, and truncation or reflection is needed to keep the variance non-negative.

| Quantity | Value |
|---|---|
| 2κθ | 0.16 |
| ξ² | 0.25 |
| Feller condition | Violated |
| Negative variance values (Euler pure) | about 2.38% |

### Fourier pricing

The Heston characteristic function and the Lewis (2001) formula price calls semi-analytically. The results agree with a 100,000-path Monte Carlo to within 0.10, and the Fourier method is far faster, but it cannot price path-dependent options.

| Strike K | Fourier | Monte Carlo | Abs. error |
|---|---|---|---|
| 80 | 23.1882 | 23.1142 | 0.0741 |
| 90 | 15.0921 | 15.0476 | 0.0446 |
| 100 | 8.3358 | 8.3466 | 0.0108 |
| 110 | 3.5706 | 3.6353 | 0.0647 |
| 120 | 1.1068 | 1.1669 | 0.0601 |

**Tech:** stochastic volatility, SDE discretisation, Monte Carlo, implied volatility inversion, Fourier methods, Python (NumPy, SciPy, Matplotlib).
