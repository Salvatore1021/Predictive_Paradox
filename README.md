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

## 🛑 Project Background & Objective

Accurate electricity demand forecasting is a critical requirement for power grid stability and efficient national energy management. The balance between power generation and consumption must be maintained in real-time. 
* **Overestimating demand** leads to excessive generation, wasted fossil fuels, and unnecessary financial losses.
* **Underestimating demand** causes severe grid instability, frequency drops, and ultimately, forced load shedding (blackouts).

**The Objective:** To build a predictive machine learning model capable of forecasting the **next hour's electricity demand (`demand_mw`)** on a national grid. 

**The Constraint:** To solve this predictive paradox strictly using **classical machine learning architectures** (e.g., tree-based regressors). Deep learning (LSTMs, Transformers) and standard autoregressive time-series packages (ARIMA, Prophet) were bypassed to demonstrate the power of pure feature engineering and ensemble learning on non-linear data.

---

## 📊 Data Sources & Integration Strategy

Instead of relying solely on historical target data, this model fuses three distinct data domains with different temporal frequencies into a single, high-frequency hourly pipeline:

1. **Grid Load Data (`PGCB_date_power_demand.xlsx`):** The primary hourly dataset containing historical energy demand, generation metrics, and fuel-specific breakdowns (gas, coal, hydro, solar).
2. **Environmental Data (`weather_data.xlsx`):** Hourly meteorological readings (temperature, humidity, precipitation) which are the primary external catalysts for heating, ventilation, and air conditioning (HVAC) load spikes.
3. **Macroeconomic Indicators (`economic_full_1.csv`):** Annual World Bank data (GDP per capita, inflation, population, renewable energy %). These establish the long-term, country-level baseline for energy consumption capability. *Integration:* Melted, pivoted, and forward-merged onto the hourly data based on the `Year`.

---

## ⚙️ The Data Pipeline & Feature Engineering

The core engine of this project is its rigorous preprocessing pipeline, designed to transform raw, fragmented data into a cohesive time-series matrix.

### 1. Anomaly & Missing Data Handling
* **Sparsity Filtering:** Features with extreme sparsity (>80% missing data, such as regional sub-grids like `india_adani` and `nepal`) were completely dropped to prevent the model from learning severe imputation biases.
* **Outlier Capping:** The target variable (`demand_mw`) exhibited extreme anomalous spikes and sudden drops characteristic of sensor errors or actual grid failures. These were neutralized by clipping values below the 1st percentile and above the 99th percentile.
* **Temporal Continuity:** Missing timestamps were aligned to a strict hourly frequency. Missing numeric power values were primarily handled via **Forward Filling (`ffill`)** to respect the chronological flow of time and prevent data leakage from the future. Minor localized gaps were smoothed using linear interpolation.

### 2. Feature Engineering Architecture

| Feature Category | Engineered Columns | Predictive Purpose |
|------------------|--------------------|--------------------|
| **Autoregressive Lags** | `lag_1`, `lag_24`, `lag_168` | Captures cyclical grid inertia (demand exactly 1 hour ago, 1 day ago, and 1 week ago). |
| **Chronological Details** | `hour`, `dayofweek`, `month`, `day` | Allows tree-based models to capture daily human routines, weekend industrial demand destruction, and macro-seasonality. |
| **Target Variable Definition**| `next_hour_demand_mw` | Engineered via `shift(-1)` on the demand column to strictly define the model's supervision task as a 1-hour forecasting horizon. |

---

## 🤖 Modeling Strategy & Validation

The pipeline utilizes an **XGBoost Regressor**, chosen for its inherent resistance to multi-collinearity, its ability to handle complex non-linear interactions between weather and time, and its computational efficiency.

### Train/Test Split
To simulate real-world forecasting conditions and prevent data leakage, the data was split strictly by time:
* **Training Set:** All historical data up to and including **2023**.
* **Testing Set:** Unseen data exclusively from **2024**.

### Evaluation Metrics & Insights
The model achieved a Mean Absolute Percentage Error (**MAPE**) of **~3.12%** on the unseen 2024 test data, proving highly reliable for day-to-day grid dispatch planning.

**Feature Importance Analysis:**
Analyzing the XGBoost decision pathways revealed what truly drives grid demand:
1. **Current Demand (`demand_mw`):** The physical inertia of the grid makes $T_0$ the strongest baseline predictor for $T_{+1}$.
2. **Immediate Trajectory (`lag_1`, `lag_24`):** The identical-hour consumption pattern from the previous day is the second most powerful anchor for the model.
3. **Time & Temperature (`hour`, `temperature`):** These features proved empirically that human daily routines (waking up, returning from work) and environmental cooling/heating needs dictate the largest volatile fluctuations in load.

---

## 📂 Project Structure

```text
├── data/
│   ├── PGCB_date_power_demand.xlsx    # Target & generation data
│   ├── weather_data.xlsx              # Environmental features
│   └── economic_full_1.csv            # Macroeconomic indicators
├── notebooks/
│   ├── EDA.ipynb                      # Visual insights, distributions & correlations
│   ├── preprocessing.ipynb            # Pipeline: Cleaning, Merging & Feature Engineering
│   └── model_train.ipynb              # XGBoost training, validation, and feature importance
├── preprocessed_data.csv              # Output of preprocessing (Auto-generated)
├── xgboost_demand_forecast.pkl        # Serialized Model (Auto-generated)
└── requirements.txt                   # Environment Dependencies

## 🚀 Setup & Installation
### Step 1: Navigate to the project root directory
```
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
