# ARIMA Stock Price Forecasting

This project applies classical time series modeling to forecast Apple Inc. (AAPL) stock prices using **ARIMA** and **SARIMAX** models. It includes a comprehensive end-to-end workflow: from raw stock data collection and preprocessing, to stationarity testing, parameter selection, model fitting, forecasting, evaluation, and visualization. It also compares non-seasonal ARIMA to seasonal SARIMAX to evaluate their effectiveness in modeling stock price behavior.

---

## Table of Contents

- [Overview](#overview)
- [Installation](#installation)
- [Workflow & Methodology](#workflow--methodology)
  - [1. Data Collection](#1-data-collection)
  - [2. Preprocessing](#2-preprocessing)
  - [3. Stationarity Testing](#3-stationarity-testing)
  - [4. Model Selection (ACF, PACF, auto_arima)](#4-model-selection-acf-pacf-auto_arima)
  - [5. ARIMA Model Training](#5-arima-model-training)
  - [6. Forecasting and Evaluation](#6-forecasting-and-evaluation)
  - [7. SARIMAX with Seasonality](#7-sarimax-with-seasonality)
- [Performance Evaluation](#performance-evaluation)
- [Key Insights](#key-insights)
- [Future Work](#future-work)

---

## Overview

The goal of this project is to predict stock price behavior using interpretable, statistically grounded time series models. Unlike complex black-box models, ARIMA/SARIMAX provide transparency, residual diagnostics, and statistical rigor. We test several parameter configurations and compare seasonality-aware SARIMAX models to simpler ARIMA setups.

---

## Installation

Clone this repo and install required dependencies with:

```bash
pip install yfinance pandas matplotlib seaborn statsmodels scikit-learn pmdarima numpy==1.26.4
```

## Workflow & Methodology

### 1. Data Collection
- **Source**: `yfinance` library  
- **Ticker**: AAPL (Apple Inc.)  
- **Date Range**: Jan 1, 2015 – Dec 31, 2023  
- **Frequency**: Daily closing prices  

---

### 2. Preprocessing
- Applied **log transformation** to stabilize variance  
- Visualized **raw and log-transformed** closing prices  
- Conducted **seasonal decomposition** to inspect trends and patterns  

---

### 3. Stationarity Testing
- Used the **Augmented Dickey-Fuller (ADF) test** to assess stationarity  
- Applied **first-order differencing** (`.diff().dropna()`) to remove trends  
- Verified stationarity through:
  - **ADF p-value ≈ 0**
  - **Test statistic** far below critical values
  - **Rolling mean and std** visually centered and consistent  

---

### 4. Model Selection (ACF, PACF, `auto_arima`)
- Plotted **Autocorrelation (ACF)** and **Partial Autocorrelation (PACF)** plots  
- Used `pmdarima.auto_arima()` to select optimal `(p, d, q)` by minimizing **AIC**  
- Conducted **residual diagnostics** to check:
  - Normality
  - Independence
  - Homoscedasticity  

---

### 5. ARIMA Model Training
- Trained two model configurations:
  - `ARIMA(0, 1, 1)` on log-transformed series  
  - `ARIMA(1, 0, 1)` on stationary log-differenced data  
- Reconstructed actual forecast prices using:
  - Cumulative sum of differenced predictions  
  - Exponentiation to revert log-scale  

---

### 6. Forecasting and Evaluation
- Forecasted on the **final 10%** of data  
- Transformed predicted log values back to original stock prices  
- Computed key performance metrics:
  - **Mean Absolute Error (MAE)**
  - **Root Mean Squared Error (RMSE)**
  - **Mean Absolute Percentage Error (MAPE)**  
- Plotted:
  - Actual vs Forecasted stock prices  
  - 95% confidence intervals  

---

### 7. SARIMAX with Seasonality
- Implemented `SARIMAX(1, 1, 1)x(1, 1, 1, 5)` to model **weekly seasonal effects**  
- Forecasts were reconstructed from **differenced log predictions**  
- Confidence intervals and predictions were:
  - Exponentiated
  - Aligned with original time index  
- Compared performance metrics and visual plots against non-seasonal ARIMA  

---

## Performance Evaluation

Model performance was assessed on the final 10% of the dataset using the following evaluation metrics:

- **Mean Absolute Error (MAE)**: Average absolute difference between actual and predicted prices.
- **Mean Squared Error (MSE)** and **Root Mean Squared Error (RMSE)**: Highlight larger prediction errors more strongly.
- **Mean Absolute Percentage Error (MAPE)**: Represents average percentage error relative to actual values.

### ARIMA(1, 0, 1)
- **MAE**: 9.78  
- **MSE**: 151.67  
- **RMSE**: 12.32  
- **MAPE**: 5.41%

### SARIMAX(1, 1, 1)x(1, 1, 1, 5)
- **MAE**: 8.22  
- **MSE**: 110.78  
- **RMSE**: 10.53  
- **MAPE**: 4.58%

The SARIMAX model, by capturing potential weekly seasonality, showed superior forecasting accuracy over both ARIMA variants across all major metrics.


## Key Insights

- **Log Transformation and Differencing** were critical to ensure stationarity, a core assumption of ARIMA-based models.
- **ARIMA(1, 0, 1)** and **ARIMA(0, 1, 1)** both provided solid baselines but had limitations in capturing sharp fluctuations in stock prices.
- **SARIMAX(1, 1, 1)x(1, 1, 1, 5)** performed better by modeling potential weekly seasonality, yielding more accurate and stable predictions.
- **Model diagnostics** (residual analysis, confidence intervals, and AIC-based selection) played a central role in verifying model validity.
- While ARIMA/SARIMAX can model linear and seasonal dynamics effectively, **they may struggle with non-linear or abrupt market changes**, suggesting the need for more complex or hybrid approaches in future work.

---

## Future Work

- Incorporate **exogenous variables (e.g., macroeconomic indicators, sentiment scores)** using the SARIMAX `exog` feature to improve predictive power.
- Explore **non-linear models**, such as **LSTM, GRU, or Transformer-based time series models**, for capturing long-term dependencies.
- Integrate **Prophet or hybrid approaches** that combine classical and ML-based models.
- Extend the framework to handle **multi-stock or portfolio forecasting** with dynamic correlations.
- Build an **interactive visualization dashboard** to experiment with parameter tuning, model outputs, and scenario simulations.
