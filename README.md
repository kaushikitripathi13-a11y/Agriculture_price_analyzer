# Agriculture_price_analyzer
Time-series forecasting of Indian mandi commodity prices using Agmarknet (data.gov.in) data. Compares SARIMA, Prophet and LightGBM against a seasonal-naive baseline, with an interactive dashboard for price forecasts and best-market comparison.
# Mandi Price Forecasting & Market Comparison Dashboard

A time-series forecasting project that predicts the **modal price (₹/quintal)** of agricultural commodities across Indian mandis, using market price history combined with local weather data, and serves the results through an interactive dashboard.

Built as a third-year data science project using open government data from **Agmarknet (data.gov.in)**.

---

## Problem Statement

Mandi prices swing day to day with arrivals, weather, festivals and local demand. Farmers and traders often decide where and when to sell with little visibility into expected prices. This project answers two questions:

1. What will the modal price of a commodity be in a given market over the next *N* days?
2. Which market is likely to give the best price?

## Data

- **Price data:** [data.gov.in – Current Daily Price of Various Commodities from Various Markets (Mandi)](https://www.data.gov.in/resource/variety-wise-daily-market-prices-data-commodity), generated from Agmarknet. Market-level records with arrival date, market, commodity and min / max / modal price.
- **Weather data:** daily weather for each market, matched using the market's latitude and longitude.
- **Scope:** 7 markets across **West Bengal** (Bishnupur, Khatra, Champadanga, Kalipur) and **Uttar Pradesh** (Gulavati, Sambhal, Puwaha) for [commodity name(s)].
- **Period:** January 2022 to October 2025.

## Approach

1. **Data preparation:** cleaned the price records, standardised market and commodity names, and merged prices with weather data on market and date.
2. **Feature engineering:** lagged prices, rolling averages, calendar features and weather variables, with a target price column for the forecast horizon. All lag and rolling features use only past information to avoid data leakage.
3. **Modelling:** two models compared:
   - **Seasonal naive** baseline (price from the same day last week)
   - **Gradient Boosting (GBM)** trained on the engineered lag and weather features
4. **Validation:** time-based train/test split, with no random shuffling. Evaluated with MAPE and MAE (₹/quintal).

## Results

| Model | MAPE | MAE (₹/quintal) |
|-------|------|-----------------|
| Seasonal Naive (baseline) | [X]% | [X] |
| Gradient Boosting | [X]% | [X] |

*Key takeaway: [e.g. Gradient Boosting reduced MAPE by X% over the seasonal-naive baseline; weather features improved / did not improve accuracy.]*

## Dashboard

Built with [Streamlit / Dash]. Features:

- Latest and historical modal prices by market
- Price trend with forecast
- Market comparison to find the best expected price

[Add a screenshot or GIF here: `docs/dashboard.png`]

## Tech Stack

Python, pandas, NumPy, scikit-learn, [LightGBM / XGBoost, if used], [Streamlit / Dash], Matplotlib / Plotly, Git

## Project Structure

```
Agriculture_price_analyzer/
├── app/             # dashboard
├── data/            # raw and processed data
├── models/          # trained models
├── notebooks/       # EDA, feature engineering, modelling
├── scripts/         # data preparation scripts
├── config.py        # file paths, columns, market coordinates
├── requirements.txt
└── README.md
```

## How to Run

```bash
git clone https://github.com/kaushikitripathi13-a11y/Agriculture_price_analyzer.git
cd Agriculture_price_analyzer
pip install -r requirements.txt

# Launch the dashboard
streamlit run app/app.py
```

## What I Learned

- Handling messy real-world public data: duplicates, gaps and inconsistent names
- Merging data from different sources (prices and weather) by market and date
- Avoiding data leakage in time-series problems
- Why a strong baseline matters: a complex model is only useful if it beats seasonal naive
- Turning a model into something a non-technical user can use

## Limitations and Future Work

- Limited to 7 markets in two states; more markets and commodities would make the model more general
- Market coordinates were looked up manually, and one (Puwaha) is approximate
- Add more external features such as crop output, festivals and fuel prices
- Try additional models such as SARIMA or Prophet for comparison

## Author

**[Your Name]**, B.Tech [Branch], [College Name], 3rd year
[LinkedIn] · [Email]
