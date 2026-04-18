# 🔮 Predictive Paradox: Short-Term Electricity Demand Forecasting

## 📌 Project Overview

Accurate electricity demand forecasting is critical for power grid stability and efficient energy management. This repository contains a robust classical machine learning pipeline designed to predict the short-term power demand (next hour's `demand_mw`) on a national grid based on historical consumption, environmental factors, and macroeconomic indicators.

## 📂 Repository Structure
```text
├── data/
│   ├── PGCB_date_power_demand.xlsx    # Hourly demand and generation data (Target)
│   ├── weather_data.xlsx              # Hourly environmental features
│   └── economic_full_1.csv            # Macroeconomic indicators
├── notebooks/
│   ├── EDA.ipynb                      # Exploratory Data Analysis & visual insights
│   ├── preprocessing.ipynb            # Data cleaning, feature engineering, and merging
│   └── model_train.ipynb              # XGBoost training, evaluation, and serialization
├── preprocessed_data.csv              # Cleaned and merged dataset for training (Generated)
├── xgboost_demand_forecast.pkl        # Serialized XGBoost model (Generated)
├── requirements.txt                   # Environment dependencies
├── README.md                          # Project Structure and Setup
└── summary.ipynb                      # Project documentation

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
