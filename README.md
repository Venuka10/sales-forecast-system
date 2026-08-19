# sales-forecast-system

Daily sales forecasting for a takeaway restaurant, built from real POS data. The goal is a working SARIMAX model that forecasts short-term demand, accounting for weekly seasonality and (eventually) weather conditions — with an eye toward using the same forecast as an input for labour cost optimization down the line.

## Data

- ~9 months of daily net sales, aggregated from POS exports across delivery platforms
- Single time series: date and daily net sales (£)
- Not included in this repo (see `.gitignore`) — sales data is excluded for privacy

## Methodology

**1. Data cleaning**
- Parsed date column, sorted chronologically, checked for and handled missing values/calendar gaps

**2. Stationarity testing**
- Augmented Dickey-Fuller (ADF) test on the raw series confirmed non-stationarity (p = 0.18)
- Applied regular differencing (d=1) to remove trend, then seasonal differencing (D=1, lag 7) to remove weekly seasonality
- Re-ran ADF on the fully differenced series — confirmed stationary (p ≈ 6.8e-9)

**3. Model order selection**
- Plotted ACF and PACF on the differenced series to identify candidate AR/MA orders
- Significant lags at 1, 2, and 7 (seasonal) — indicating a mixed ARMA process with weekly seasonal structure
- Comparing candidate SARIMA(p,d,q)(P,D,Q,7) configurations by AIC/BIC

**4. Model fitting**
- Baseline: `SARIMAX(1,1,1)(1,1,1,7)` — AIC 3512.35, BIC 3530.08
- Iterating on seasonal AR term (found not statistically significant in baseline — testing leaner configurations)

## Planned next steps

- Residual diagnostics (Ljung-Box) on the selected model
- Walk-forward validation against a naive baseline
- Add weather (cloudy/sunny/rain) as an exogenous variable via SARIMAX
- Trend decomposition (STL) for isolating and quantifying genuine business growth vs. seasonal/noise effects

## Setup

\`\`\`bash
python -m venv venv
venv\Scripts\activate          # Windows
pip install -r requirements.txt
\`\`\`

## Requirements

- pandas
- numpy
- matplotlib
- statsmodels

## Status

Active development — SARIMA baseline fitted, SARIMAX with weather in progress.
