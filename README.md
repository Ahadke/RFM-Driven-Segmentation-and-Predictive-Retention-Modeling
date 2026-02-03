# Enterprise-Scale Causal & Probabilistic Demand Forecasting System

## Project Overview

This project implements an **enterprise-scale demand forecasting system** designed to mirror real-world forecasting pipelines used in large retail and technology organizations. The system integrates **causal feature engineering**, **probabilistic forecasting**, **hierarchical reconciliation**, **inventory optimization**, and **production-style monitoring** into a single end-to-end workflow.

Rather than producing only point forecasts, the system explicitly models **demand uncertainty**, ensures **consistency across hierarchical levels**, and translates forecasts into **actionable inventory decisions**, while continuously monitoring performance for potential concept drift.

---

## Objectives

The key objectives of this project are to:

- Forecast retail demand at the **SKU–store level**
- Quantify **forecast uncertainty** using probabilistic models
- Ensure **hierarchical consistency** across SKU, store, and state levels
- Convert forecasts into **inventory decisions** under service-level constraints
- Monitor forecast performance and detect **concept drift**

---

## Dataset Description

**Dataset:** M5 Forecasting Dataset  
**Source:** Kaggle  
**Link:** https://www.kaggle.com/competitions/m5-forecasting-accuracy  

The dataset represents historical daily sales data from a large US retailer and includes:

- SKU-level daily unit sales across multiple stores and states  
- Hierarchical product structure (item → department → category)  
- Store and regional identifiers (store → state)  
- Calendar data with holidays, events, and SNAP indicators  
- Weekly selling prices for modeling price elasticity  

Only training-period data was used to avoid any form of data leakage.

---

## Data Preparation & Transformation

The raw datasets were transformed to support large-scale time-series analysis:

- Converted sales data from wide format to long format for temporal modeling  
- Merged calendar data to attach real dates, events, and promotions  
- Created unique SKU–store identifiers to represent individual demand time series  
- Sampled SKU–store series during development to manage memory constraints  
- Ensured proper temporal alignment across sales, calendar, and price data  

This resulted in a clean, time-indexed dataset suitable for feature engineering and forecasting.

---

## Causal Feature Engineering

Demand drivers were explicitly modeled rather than relying only on autoregressive signals.

### Temporal Features
- Lagged demand features (7, 14, 28 days) to capture weekly and monthly patterns  
- Rolling mean and rolling standard deviation to smooth short-term volatility  

### Price & Promotion Features
- Relative price change features to approximate price elasticity  
- SNAP indicators to model region-specific promotional effects  

### Calendar & Event Features
- Event flags derived from calendar data  
- Week, month, and year indicators to capture seasonality  

### Demand Stability Features
- Intermittent-demand flags to distinguish sparse vs stable demand behavior  

All features were engineered using only information available prior to the prediction date.

---

## Probabilistic Demand Forecasting

Instead of predicting a single demand value, the system produced **full demand distributions**.

- Global quantile regression models were trained across all SKU–store time series  
- Forecasts were generated for multiple quantiles (P10, P50, P90)  
- Time-aware train–test splitting was used to prevent look-ahead bias  
- Prediction intervals were evaluated using empirical coverage  

### Key Results
- **Mean Absolute Error (MAE):** ~1.07 units (P50 forecast)  
- **P10–P90 Coverage:** ~87.2%, indicating well-calibrated uncertainty  

---

## Hierarchical Forecast Reconciliation

Independent SKU-level forecasts were reconciled to ensure consistency across the demand hierarchy:

- SKU-level forecasts were aggregated to store and state levels  
- Bottom-up aggregation preserved detailed forecast structure  
- Reconciliation enforced coherence without distorting individual SKU forecasts  

This ensured alignment between operational and planning-level demand views.

---

## Inventory Optimization

Probabilistic forecasts were translated into operational inventory decisions:

- Median forecasts (P50) represented expected demand  
- Forecast uncertainty was approximated using the spread between quantiles  
- Safety stock was computed using a service-level constraint  
- Reorder points were calculated as expected demand plus safety stock  

Low-demand and intermittent SKUs naturally resulted in lower reorder points, demonstrating demand-aware inventory behavior.

---

## Drift Detection & Monitoring

To simulate production deployment, forecast performance was continuously monitored:

- Forecast errors were computed at the SKU level  
- Rolling error statistics were used to smooth noise  
- Threshold-based rules flagged potential concept drift  
- System-wide drift rates were tracked over time  

During the evaluation window, no sustained drift was detected, indicating stable forecast performance and no false-positive alerts.

---

## Results & Conclusions

- The system achieved **strong point forecast accuracy** despite sparse SKU-level demand  
- **Prediction intervals were well calibrated**, slightly conservative but operationally robust  
- Hierarchical reconciliation ensured **consistent aggregation across levels**  
- Inventory policies dynamically adapted to demand uncertainty  
- Drift monitoring provided reliable signals without over-alerting  

Overall, the project demonstrates a **production-ready demand forecasting pipeline** that balances accuracy, uncertainty, interpretability, and operational relevance.

---

## Technologies & Libraries Used

### Programming & Analysis
- Python  
- Jupyter Notebook  

### Core Libraries
- pandas – data manipulation and aggregation  
- numpy – numerical computation  
- matplotlib – visualization  
- scikit-learn – machine learning models  
- scipy – statistical calculations  

---

## Project Structure
```
├── data/
│   └── online_retail_II.csv
├── notebooks/
│   ├── 01_data_loading_cleaning.ipynb
│   ├── 02_exploratory_data_analysis.ipynb
│   ├── 03_feature_engineering_rfm.ipynb
│   ├── 04_customer_segmentation.ipynb
│   ├── 05_retention_cohort_analysis.ipynb
│   ├── 06_visualization_and_insights.ipynb
│   └── 07_conclusions.ipynb
├── outputs/
│   ├── clean_retail_data.csv
│   ├── rfm_table.csv
│   └── rfm_with_clusters.csv
├── README.md
└── requirements.txt
```
