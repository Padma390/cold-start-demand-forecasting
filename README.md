# Cold Start Forecasting

This project explores demand forecasting for new or low-history retail products.
It uses the M5-style Walmart sales data and compares two routes:

## Business Problem

Forecasting models rely on historical sales to learn demand patterns. However, newly launched products have little or no sales history, making traditional forecasting approaches ineffective.

This project explores a semantic retrieval-based cold-start forecasting framework that estimates demand for new products by transferring demand patterns from similar existing products.

## Solution Overview

- **Route A: Normal forecasting** for products with enough history.
- **Route B: Cold-start forecasting** for products where only the first few days of sales are visible.

The cold-start route retrieves similar existing products using semantic embeddings built from product attributes and demand behavior, then transfers Route A model forecasts from those similar products. This avoids leaking same-date actual sales from peer products into the cold-start forecast.

## Dataset

This project uses the M5 Forecasting dataset.
The raw data is not included in this repository due to GitHub's file size limits. Download the dataset and place the required CSV files in `data/raw/`.

## Project Structure

```text
.
├── notebooks/
│   ├── 01_baseline_hybrid_forecasting.ipynb
│   └── 02_store_item_level_cold_start_forecasting.ipynb
├── data/
│   └── raw/
        ├── calendar.csv
        ├── sales_train_validation.csv
        └── sell_prices.csv
├── outputs/
│   ├── item_level
│   └── store_item_level
├── requirements.txt
└── README.md
```

## Repository

01_baseline_hybrid_forecasting.ipynb

• Route A forecasting
• Cold-start simulation
• Item-level semantic retrieval
• Baseline comparison

02_store_item_level_cold_start_forecasting.ipynb

• Store-item semantic retrieval
• Store-aware embeddings
• Store-level baseline comparison


## Method

1. Load recent sales, calendar, and price data.
2. Sample a smaller product/store subset for fast iteration.
3. Simulate cold-start products by hiding sales after the first `visible_days`.
4. Train Route A XGBoost model using lag, rolling, calendar, price, product, and store features.
5. Build product embeddings from category, department, price band, demand band, volatility, and intermittency.
6. Tune `top_k` across candidate similarity-neighbor counts.
7. Compare cold-start forecasts against simple baselines.


## Key Results

The semantic retrieval framework was evaluated against
simple baselines.

Compared methods:

- Category Average
- Department Average
- Store-Category Average
- Visible Launch Average
- Semantic Top-K Transfer

Item-level semantic retrieval outperformed category, department and store-category averages, while the visible launch baseline remained the strongest simple benchmark.

Store-item semantic retrieval increased granularity but did not outperform the item-level approach on the sampled dataset.


## Limitations

- Simulated cold-start scenario
- Product metadata limited to structured attributes
- Promotions and launch events unavailable
- Single dataset and limited sample size


## Future Work

- Hyperparameter tuning for Route A
- Larger sample / full M5 dataset evaluation
- Bayesian updating using first-week sales
- Incorporate launch promotions (Promotion priors were not implemented because launch promotion metadata was not available in the dataset)
- Richer product descriptions
- Store clustering instead of raw store IDs
- Power BI dashboard


## Notes

Top-K is selected on the simulated cold-start holdout. In production, this would be tuned using historical launch backtests

The current semantic embedding uses structured product and demand attributes. A stronger future version would add real product text such as brand, title, pack size, ingredients, or product description.

For a more robust final result, repeat the cold-start simulation across many random seeds and larger product/store samples.
