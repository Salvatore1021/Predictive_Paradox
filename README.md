<p align="center">
  <h1 align="center">⚡ Predictive Paradox: Power Demand Forecasting</h1>
  <p align="center">
    <em>A robust classical machine learning pipeline for short-term national grid load prediction.</em>
  </p>
  <p align="center">
    <img src="https://img.shields.io/badge/python-3.8+-blue" alt="Python 3.8+">
    <img src="https://img.shields.io/badge/model-XGBoost-orange" alt="XGBoost">
    <img src="https://img.shields.io/badge/domain-Time%20Series-brightgreen" alt="Time Series">
  </p>
</p>

---

## 🛑 The Problem

Accurate electricity demand forecasting is critical. Overestimating demand leads to wasted generation and financial loss, while underestimating causes severe grid instability and load shedding. Standard autoregressive models often fail to capture the complex, non-linear relationships between human routines, sudden weather shifts, and macro-economic constraints.

## 💡 The Approach

This project strictly utilizes classical machine learning architectures (XGBoost) to predict the **next hour's `demand_mw`**. Instead of relying solely on historical target data, the model fuses three distinct data domains into a single high-frequency pipeline:

| Data Domain | Features Used | Purpose | Time Complexity |
|-------------|---------------|---------|:---:|
| **Grid Load** | `demand_mw`, `generation_mw` | Immediate power requirements and inertia. | Hourly |
| **Environmental** | `temperature`, `humidity`, `precipitation` | Captures HVAC/cooling load spikes. | Hourly |
| **Macroeconomic**| `GDP`, `Inflation`, `Energy %` | Anchors long-term country-level consumption capability. | Annual (Merged) |

---

## ⚙️ The Data Pipeline

The core of this engine is its rigorous preprocessing and feature engineering.

### 1. Anomaly & Missing Data Handling
* **Sparsity Filtering:** Features with >80% missing data (`india_adani`, `nepal`) are dropped to prevent imputation bias.
* **Outlier Capping:** Extreme grid faults or sensor errors in `demand_mw` are clipped at the 1st and 99th percentiles.
* **Temporal Continuity:** Missing values in power features are handled via Forward Filling (`ffill`) to prevent data leakage from the future, supplemented by linear interpolation for localized gaps.

### 2. Feature Engineering Architecture

| Feature Type | Engineered Columns | What it catches |
|--------------|--------------------|-----------------|
| **Autoregressive** | `lag_1`, `lag_24`, `lag_168` | Cyclical grid inertia (1 hour ago, 1 day ago, 1 week ago). |
| **Chronological** | `hour`, `dayofweek`, `month` | Human routines, weekend demand destruction, seasonality. |
| **Target Setup** | `next_hour_demand_mw` | Uses `shift(-1)` to strictly define the 1-hour forecasting horizon. |

---

## 📊 Model Evaluation & Insights

The pipeline trains an **XGBoost Regressor** on data up to 2023 and evaluates purely on unseen 2024 data. 

**Performance:** The model achieves a Mean Absolute Percentage Error (MAPE) of **~3.12%**.

**Key Drivers (Feature Importance):**
1. **Current Demand (`demand_mw`):** The grid's physical inertia makes $T_0$ the strongest predictor for $T_{+1}$.
2. **Immediate Lags (`lag_1`, `lag_24`):** Identical-hour patterns from the previous day dictate the trajectory.
3. **Time & Temp (`hour`, `temperature`):** Empirically proves that time-of-day routines and environmental cooling needs are the defining external catalysts.

---

## 📂 Project Structure

```text
├── data/
│   ├── PGCB_date_power_demand.xlsx    # Target & generation data
│   ├── weather_data.xlsx              # Environmental features
│   └── economic_full_1.csv            # Macroeconomic indicators
├── notebooks/
│   ├── EDA.ipynb                      # Visual insights & distributions
│   ├── preprocessing.ipynb            # Pipeline: Cleaning & Engineering
│   └── model_train.ipynb              # XGBoost training & evaluation
├── preprocessed_data.csv              # Output of preprocessing (Auto-generated)
├── xgboost_demand_forecast.pkl        # Serialized Model (Auto-generated)
└── requirements.txt                   # Dependencies
```

## 🚀 Setup & Installation
### Step 1: Navigate to the project root directory

```bash

cd path/to/Predictive_Paradox

```
### Step 2: Create and activate virtual environment
**On Windows:**

```bash

python -m venv venv

venv\Scripts\activate

```
**On macOS/Linux:**

```bash

python3 -m venv venv

source venv/bin/activate

```
### Step 3: Install dependencies

```bash

pip install -r requirements.txt

```
