# Agriculture_price_analyzer
Time-series forecasting of Indian mandi commodity prices using Agmarknet (data.gov.in) data. Compares SARIMA, Prophet and LightGBM against a seasonal-naive baseline, with an interactive dashboard for price forecasts and best-market comparison.
# Mandi Price Forecasting & Market Comparison Dashboard

A time-series forecasting project that predicts the **daily modal price (₹/quintal)** of agricultural commodities across Indian mandis, compares markets, and serves the results through an interactive dashboard.

Built as a third-year data science project using open government data from **Agmarknet (data.gov.in)**.

---

## Problem Statement

Mandi prices swing day to day with arrivals, weather, festivals and local demand. Farmers and traders often decide where and when to sell with little visibility into expected prices. This project answers two questions:

1. What will the modal price of a commodity be in a given market over the next *N* days?
2. Which nearby market is likely to give the best price?

## Data

- **Source:** [data.gov.in – Current Daily Price of Various Commodities from Various Markets (Mandi)](https://www.data.gov.in/resource/variety-wise-daily-market-prices-data-commodity), generated from Agmarknet.
- **Fields:** state, district, market, commodity, variety, grade, arrival date, min / max / modal price.
- **Scope:** [2–3 commodities, e.g. Wheat, Onion, Tomato] across [5–10 markets in states].
- **Collection:** The API only serves the current day, so I wrote a collector (`mandi_collector.py`) that pulls and archives prices daily. Older history was combined from Agmarknet price reports.

## Approach

1. **Data collection and merging:** Python collector with pagination and retries; combined multiple downloads with `pd.concat`, then de-duplicated on market, commodity, variety and arrival date and standardised names.
2. **Cleaning and imputation:**
   - Prices outside the reported [min, max] range treated as errors and set to NaN.
   - Unit slips (prices ~100× too high) caught with a per-commodity IQR check.
   - Market-closed days kept as genuine gaps rather than imputed; only short gaps (≤3 days) filled by interpolation.
   - Series with under 80% of days present were dropped.
3. **Feature engineering:** lags (1, 7, 14, 30 days), 7- and 30-day rolling mean and standard deviation (computed after `shift(1)` to avoid leakage), day of week, month, festival flags, price spread (max − min), and lagged national average price.
4. **Modelling:** compared models in increasing complexity:
   - Seasonal naive (same day last week) as the baseline
   - LightGBM across all markets on lag features
5. **Validation:** `TimeSeriesSplit` with evaluation on the last 4–8 weeks. No random splits, and no same-day min/max used to predict that day's modal price.

## Results

| Commodity | Baseline (Seasonal Naive) MAPE | Best Model | Best Model MAPE | MAE (₹/quintal) |
|-----------|-------------------------------|------------|-----------------|-----------------|
| [Wheat]   | [X]%                          | [Model]    | [X]%            | [X]             |
| [Onion]   | [X]%                          | [Model]    | [X]%            | [X]             |
| [Tomato]  | [X]%                          | [Model]    | [X]%            | [X]             |

*Key takeaway: [e.g. staples were forecast more accurately than perishables, which are far more volatile; the tree-based model beat the baseline by X% on MAPE.]*

## Dashboard

Built with [Streamlit / Dash]. Features:

- **KPIs:** latest modal price, 7-day change %, price spread, cheapest and costliest mandis, markets reporting today
- **Charts:** price trend with forecast band, price by state, commodity × market heatmap
- **Estimation panel:** choose a commodity, market and horizon to get a forecast with a prediction interval, plus the market with the best expected price

[Add a screenshot or GIF here: `docs/dashboard.png`]

## Tech Stack

Python, pandas, NumPy, statsmodels / pmdarima, Prophet, LightGBM, scikit-learn, Streamlit, Matplotlib / Plotly, Git

## Project Structure

```
mandi-forecast/
├── notebooks/          # EDA, feature engineering, modelling
├── dashboard/          # app.py
├── mandi_collector.py  # daily data collector
├── requirements.txt
└── README.md
```

## How to Run

```bash
git clone https://github.com/YOUR_USERNAME/mandi-forecast.git
cd mandi-forecast
pip install -r requirements.txt

# Collect data (free key from data.gov.in)
export DATAGOV_API_KEY="your_key"
python mandi_collector.py

# Launch the dashboard
streamlit run dashboard/app.py
```

## What I Learned

- Handling messy real-world public data: duplicates, unit errors and structural gaps
- Spotting and avoiding data leakage in time-series problems
- Why a strong baseline matters: a complex model is only useful if it beats seasonal naive
- Turning a model into something a non-technical user can use

## Limitations and Future Work

- History is limited by when collection started; more data would improve long-lag and seasonal models
- Add external features such as rainfall, crop output and fuel prices
- Cluster mandis by price behaviour
- Automate daily collection and retraining with a scheduled job

## Author

**[Kaushiki Tripathi]**, B.Tech [CSE], [ABES ENGINEERING COLLEGE], 3rd year
[KaushikiTripathi] · [kaushikitripathi13@gmail.com]
