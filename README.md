# Suicide Rates in the United States, 1999–2020

A time-series study of US monthly suicide rates: testing for stationarity,
separating trend from seasonality, fitting ARMA and SARIMA models by
information criterion, and forecasting two years ahead.

Course project for **SF2943 Time Series Analysis**, KTH, spring 2022.
Thomas Bouquet · Rodrigo Castellano Ontiveros · Alejandro Garcia Castellanos ·
Paul Lieutier · Luca Marini

📄 **[Read the report](report/report.pdf)** ·
[paper review (Part B)](report/paper-review.pdf)

## The data

Monthly suicide counts, 1999–2020, converted to a **rate** using US monthly
population (`POPTHM`, from FRED). Using the rate rather than raw counts matters:
the population grew substantially over the period, so absolute counts would
show a rising trend that is partly just more people.

```
data/usa-suicide-rate-1999-2020.csv
data/usa-population-monthly.csv
```

## What the analysis does

**Establishing non-stationarity.** A 12-month rolling mean and standard
deviation show a non-constant trend, and the Dickey–Fuller test gives
*p* = 0.795 — nowhere near rejecting a unit root.

**Seasonality.** Overlaying the years makes the annual pattern obvious: the
same shape recurs, with a pronounced drop every February.

**Decomposition.** Additive and multiplicative decompositions were both fitted
and compared by the Dickey–Fuller *p*-value of their residuals — additive won
(1.68e-13 against 8.36e-13), so that is the model used.

**Differencing.** A lag-12 difference removes the seasonality (*p* = 1.26e-03),
but the mean is still not quite constant; one further first difference gives
*p* = 8.77e-08 and a stable mean.

**Model selection by AICc.** A grid search over ARMA(p,q) with both orders from
0 to 20 selected **MA(14)** — but most of its coefficients have high *p*-values,
which the report reads as overfitting rather than a real fourteenth-order
dependence. A stepwise `auto_arima` search then found
**SARIMA(1,1,1)×(1,0,1)₁₂**, with AICc **−1026.30**, far below the ARMA model,
and coefficient *p*-values near zero throughout.

Residuals of both models are normally distributed, so the fitted parameters are
trustworthy.

**Forecasts** for 2021 and 2022 from both models are close to each other; they
differ mainly in the width of the confidence bands.

## Running

```bash
pip install numpy pandas matplotlib statsmodels pmdarima
jupyter notebook analysis.ipynb
```

The notebook reads both CSVs from `data/`.
