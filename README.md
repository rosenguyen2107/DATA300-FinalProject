# Machine Learning for Short-Term Volatility Prediction
### A Comparative Study of Classification Models and Trading Performance

> **DATA 300: Statistical and Machine Learning** · Dickinson College · Spring 2026  
> **Authors:** Jules Dao · Rose Nguyen · Group 1

---

## Overview

This project investigates the use of supervised machine learning to predict short-term equity market volatility across S&P 500 constituents. Rather than predicting return direction, we frame the problem as a regression task in which models forecast **next-day realized volatility** from a set of engineered technical features.

Five models are compared against a naive baseline:

| Model | Type |
|---|---|
| Lasso Regression ★ | Linear, L1 regularization |
| Ridge Regression | Linear, L2 regularization |
| Gradient Boosting | Ensemble (sequential trees) |
| Random Forest | Ensemble (bagged trees) |
| Decision Tree | Single tree |

Predictions are then embedded into a **volatility-parity portfolio backtesting framework** to test whether better accuracy translates into better risk-adjusted returns.

---

## Key Results

| Strategy | Total Return | Sharpe Ratio | Max Drawdown |
|---|---|---|---|
| Equal Weight (benchmark) | 34.17% | 1.24 | −15.38% |
| V2 Vol + VaR | 36.94% | 1.45 | −11.19% |
| V3 Lasso | 54.94% | 2.08 | −7.49% |
| **V3 Ridge ★** | **55.17%** | **2.08** | **−7.48%** |

Best predictive model: **Lasso Regression** (Test R² = 0.756, RMSE = 0.00653)

---

## Data

- **Source:** Yahoo Finance via `yfinance`
- **Universe:** S&P 500 constituents + `^GSPC`, `^IRX`
- **Period:** January 2, 2015 – April 23, 2026
- **Observations:** ~1,381,187 stock-day records
- **Variables:** Daily OHLCV (open, high, low, close, volume)

---

## Features

| Feature | Correlation with Target | Description |
|---|---|---|
| `ewma_vol` | +0.888 | EWMA volatility, span = 10 |
| `vol_10` | +0.828 | 10-day rolling std of returns |
| `vol_lag_1` | +0.827 | Lagged volatility (t−1) |
| `vol_lag_2` | +0.742 | Lagged volatility (t−2) |
| `r_pos_5` | +0.662 | Cumulative positive return, 5 days |
| `r_neg_5` | −0.756 | Cumulative negative return, 5 days |
| `return_lag_1` | −0.047 | Log return at t−1 |
| `return_lag_2` | −0.062 | Log return at t−2 |
| `return_10` | −0.174 | 10-day cumulative return |

**Target:** `target_vol` = 5-day rolling standard deviation of log returns, shifted +1 day

## Methodology

### Train / Test Split
Strictly chronological 80/20 split — no random shuffling.
- **Train:** January 27, 2015 → February 14, 2024
- **Test:** February 14, 2024 → April 23, 2026

### Model Selection
5-fold time-series cross-validation with temporal ordering preserved. Primary criterion: lowest CV RMSE mean.

### Backtesting
Volatility-parity framework: each stock is weighted by the inverse of its predicted next-day volatility (`wᵢ ∝ 1/σ̂ᵢ`). Realistic features include 0.1% transaction costs, 5-day rebalance frequency, 20% single-stock cap, VaR constraint, momentum filter, and volatility-regime detector.

---

## Limitations

- S&P 500 only — large-cap bias, results may not generalize to small-cap or international markets
- Training period is predominantly a bull market — limited bear-market exposure
- Fixed train/test split — model never retrains as market evolves
- Volatility regime plot uses cross-sectional averaging, which smooths out individual-stock spikes

---

## Future Work

- GARCH / LSTM benchmarks for formal comparison
- Walk-forward backtesting with periodic retraining
- Stress-test on 2008 financial crisis, COVID crash, 2022 rate-shock periods
- Add options-implied volatility as a forward-looking feature

---

## References

- Yang (2023). *ICFTBA Proceedings.*
- Ghysels, Santa-Clara & Valkanov (2004). MIDAS.
- Bollerslev (1986). *Journal of Econometrics* — GARCH.
- Nelson (1991). *Econometrica* — EGARCH.
- Asness, Frazzini & Pedersen (2012). Risk Parity.
- Salt Financial (n.d.). *Volatility Forecasting Guide.*

---

## License

This project was completed for academic purposes at Dickinson College. Not intended for commercial use.
