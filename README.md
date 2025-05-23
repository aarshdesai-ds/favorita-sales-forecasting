# 🛒 Favorita Sales Forecasting

This project forecasts daily product family sales for Favorita grocery stores in Ecuador using two distinct modeling approaches:

- 🌲 **Random Forest Regressor** for multivariate row-level predictions
- ⏳ **SARIMAX** for univariate time series forecasting of total daily sales

It compares their accuracy, explains the feature engineering and evaluation process, and prepares Kaggle-style submission files.

---

## 📦 Dataset Overview

**Source**: [Kaggle Store Sales - Time Series Forecasting](https://www.kaggle.com/competitions/store-sales-time-series-forecasting)

The dataset includes:
- `train.csv` – Historical daily sales with store, family, and promotion data
- `test.csv` – Future dates for which predictions are required (without target)
- `transactions.csv` – Daily transaction counts per store
- `holidays_events.csv` – Public holidays and transferred observances
- `oil.csv` – Global oil prices (Ecuador’s economy is oil-sensitive)
- `stores.csv` – Metadata for each store (city, state, type, cluster)

---

## 🧠 Modeling Approaches

### 🌲 1. Random Forest Regressor

A machine learning model trained on a wide set of engineered features:

- Calendar-based: `day`, `month`, `year`, `dayofweek`, `is_weekend`
- Promotion and holiday indicators: `onpromotion`, `is_holiday`
- Transactions: store-level daily sales volume
- Lag features: `sales_lag_7` for validation only (not used in final test model)

We trained:
- `rf_model` with full feature set (used for evaluation)
- `rf_model_no_lag` for production (`test.csv`), excluding unavailable lag features

### ⏳ 2. SARIMAX (Statistical Time Series)

A seasonal ARIMA model trained on total daily sales (aggregated over all stores and product families). Forecasted 15 days ahead, then mapped to all `test.csv` rows by date.

- Order: (1, 1, 1)
- Seasonal Order: (1, 1, 1, 7) → weekly seasonality

---

## 📊 Feature Engineering

- Merged datasets: holidays, stores, oil prices, transactions
- Time-based features:
  - `day`, `month`, `year`, `dayofweek`
  - `is_weekend` → based on Saturday/Sunday
  - `is_holiday` → binary flag from merged calendar
- Promotion and transaction fill:
  - `onpromotion` and `transactions` null values filled forward
- Categorical encoding: `family` converted to numerical codes
- Lagged features: `sales_lag_7` for store-family-level trends (used only during evaluation)

---


---

## 📈 Evaluation Results (on last 15 days of `train.csv`)

| Model         | MSE        | MAE     | R² Score |
|---------------|------------|---------|----------|
| Random Forest | **74,927** | **79.45** | **0.9516** |
| SARIMAX       | 22.6B      | 118,730 | -0.816   |

---

## 📤 Submissions

| File Name                 | Model               | Purpose                             |
|--------------------------|---------------------|--------------------------------------|
| `submission.csv` | ✅ Random Forest    | Final production-ready model         |
| `submission_sarimax.csv` | ❌ SARIMAX           | Benchmark (daily total → per-row)    |

> All predictions are **rounded to 2 decimal places**.

---

## 📌 Key Insights

- ✅ Random Forest performed **extremely well**, capturing 95% of sales variance and generalizing accurately across stores and families.
- ❌ SARIMAX failed to model disaggregated multivariate data and performed worse than naive averages.
- 🔁 Lag features improved model accuracy but were excluded from production due to unavailability in unseen dates.
- 🔍 Merging external data like transactions and holidays significantly boosted model performance.

---

## ✅ Final Verdict

The **Random Forest model without lag** is the most reliable, scalable, and accurate model for this task.  
It should be used in production or further refined using:

- 🧠 Advanced tree ensembles (XGBoost, LightGBM)
- 📊 Model stacking or ensembling
- 🔄 Time-aware cross-validation strategies
- 🔮 Deep learning models like LSTM or Transformer

---

## 👤 Author

**[Your Name](https://github.com/your-username)**  
Robotics & Machine Learning Enthusiast | Forecasting | Data Fusion | Computer Vision

---

## 💡 Note

Large datasets (`train.csv`, `test.csv`, etc.) are **not included in this repo**.  
Please download them from the [Kaggle competition page](https://www.kaggle.com/competitions/store-sales-time-series-forecasting).

---
