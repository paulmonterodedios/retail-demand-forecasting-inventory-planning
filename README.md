# Retail Demand Forecasting & Inventory Planning

End-to-end retail analytics project combining **demand forecasting, model explainability and inventory planning** to support data-driven replenishment decisions.

## Project Overview

The objective of this project is to forecast daily product demand and translate those predictions into practical inventory decisions.

The analysis focuses on 20 products from a representative retail store and follows a complete analytics workflow:

- Exploratory data analysis and time-series diagnostics
- Feature engineering with lag, rolling and calendar variables
- Time-based model validation
- Comparison of multiple forecasting models
- Model explainability with SHAP
- Forecast-driven safety stock and replenishment planning
- Value-based ABC inventory classification
- Operational prioritization based on commercial value and forecast uncertainty

## Business Problem

Retail inventory decisions require balancing two competing risks:

- **Stockouts**, which may result in lost sales and lower service levels
- **Excess inventory**, which increases stock exposure and operational inefficiency

This project investigates how machine learning forecasts can be used not only to predict demand, but also to improve replenishment and product prioritization decisions.

## Dataset

The dataset contains **76,000 daily observations** across:

- 5 stores
- 20 products
- 5 product categories
- 760 days
- Period: January 2022 – January 2024

Main variables include demand, price, discounts, promotions, inventory levels, units sold, units ordered, seasonality and other contextual information.

For the detailed modeling stage, store `S003` was selected as a representative store, resulting in **15,200 observations across 20 products**.

> **Dataset source:** Add the original dataset URL here.  
> The dataset is distributed under the **Apache License 2.0**.

## Methodology

### 1. Exploratory Data Analysis

The analysis examined:

- Demand distributions and product variability
- Store-level demand behavior
- Outliers and promotional effects
- Autocorrelation and periodic patterns
- Stationarity
- Relationships between demand and commercial variables

A notable finding was a strong **~76-day cyclical pattern** for product `P0006`.

### 2. Feature Engineering

The forecasting dataset includes:

- Demand lags: 1, 2, 7, 14, 30 and 76 days
- Rolling means: 7, 14 and 30 days
- 7-day rolling standard deviation
- Calendar variables
- Product and category information
- Price
- Discount
- Promotion
- Seasonality

Inventory-related variables such as `Inventory Level`, `Units Sold` and `Units Ordered` were excluded from the forecasting model to avoid temporal ambiguity and potential leakage.

### 3. Forecasting Models

Models were evaluated using a chronological train/test split.

| Model | MAE | RMSE | MAPE | R² |
|---|---:|---:|---:|---:|
| Naive Baseline | 39.80 | 51.64 | 51.97% | -0.360 |
| Linear Regression | 15.87 | 21.62 | 25.22% | 0.762 |
| Random Forest | 16.36 | 23.09 | 27.00% | 0.728 |
| Tuned Random Forest | 16.35 | 23.11 | 27.06% | 0.728 |
| **XGBoost** | **10.72** | **14.06** | **17.70%** | **0.899** |

**XGBoost achieved the strongest overall forecasting performance.**

## Model Explainability

Feature importance and SHAP analysis were used to understand model behavior.

Relevant predictive signals included:

- Price
- Discount
- Promotion
- Recent rolling demand
- Product identity
- Product category
- Seasonality

These relationships are interpreted as **predictive associations rather than causal effects**.

## Inventory Planning

Forecasts were translated into a simple replenishment policy:

**Target Stock = Forecast Demand + Safety Stock**

**Recommended Order = max(Target Stock − Previous Inventory, 0)**

Product-specific safety stocks were estimated from out-of-sample forecasting errors generated inside the training period.

### Safety Stock Results

Using a **95th-percentile forecast-error safety stock policy**:

- Service level increased from **91.09% to 98.80%**
- Total shortages decreased from **3,200 to 226 units**
- Shortages were reduced by approximately **92.9%**
- Average excess stock increased only slightly in the retrospective simulation

The 95th-percentile policy was selected as a service-oriented scenario, not as a mathematically optimal inventory policy.

## ABC Inventory Classification

Two ABC approaches were explored.

### Demand-Volume ABC

Demand was relatively evenly distributed across the product portfolio, resulting in limited differentiation.

### Value-Based ABC

A second classification used:

**Forecasted Sales Value = Predicted Demand × Price**

This produced a more useful commercial segmentation:

- **Class A:** 12 products representing ~80.2% of forecasted sales value
- **Class B:** 5 products representing ~16.2%
- **Class C:** 3 products representing ~3.6%

Finally, ABC classification was combined with relative forecast uncertainty to support operational prioritization.

`P0009` was identified as the only **Critical** SKU because it combines:

- High expected commercial value
- High relative forecasting uncertainty

## Key Takeaways

- XGBoost substantially outperformed both the naive baseline and the other machine learning models.
- Forecast accuracy varied across products, highlighting the importance of product-specific uncertainty management.
- Forecast-driven safety stocks significantly improved simulated service levels and reduced shortages.
- Value-based ABC classification provided more useful prioritization than demand volume alone.
- Combining commercial importance with forecast uncertainty created a more operationally meaningful inventory-priority framework.

## Technologies

- Python
- Pandas
- NumPy
- Matplotlib
- SciPy
- Statsmodels
- Scikit-learn
- XGBoost
- SHAP
- Jupyter Notebook

## Repository Structure

```text
retail-demand-forecasting-inventory-planning/
│
├── README.md
├── notebooks/
│   └── retail_demand_forecasting_inventory_planning.ipynb
├── images/
│   ├── forecast_vs_actual.png
│   └── model_comparison.png
├── data/
│   └── README.md
├── requirements.txt
└── .gitignore
```

## How to Run

1. Clone the repository.
2. Install the required Python packages:

```bash
pip install -r requirements.txt
```

3. Download the dataset from the original source and place it in the appropriate local data directory.
4. Open the notebook:

```bash
jupyter notebook
```

5. Run the notebook from top to bottom.

## Limitations

- The detailed modeling stage focuses on one representative store.
- Forecasting is designed as a **one-day-ahead** problem.
- Supplier lead times are not available.
- Holding, ordering and stockout costs are not included.
- Replenishment assumes inventory can be made available before same-day demand.
- The ABC analysis uses selling price rather than unit cost.
- Inventory recommendations should therefore be interpreted as an analytical planning scenario rather than a production-ready optimization system.

## Future Work

Possible extensions include:

- Multi-store forecasting
- Multi-step forecasting horizons
- Probabilistic demand forecasting
- Dynamic safety stock policies
- Supplier lead times and service constraints
- Holding and stockout cost optimization
- Model monitoring and retraining
- Additional time-series or deep learning approaches

---

**Author:** Paul Montero  
**Focus:** Data Analytics · Business Analytics · Demand Forecasting · Inventory Planning
