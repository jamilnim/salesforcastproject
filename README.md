# Evaluating Machine Learning Models for Weekly Retail Sales Forecasting

**Project report:** Walmart Store Sales Forecasting dataset  
**Prepared by:** Muhammad Jamil  
**Programme:** Big Data Analysis, Arcada University of Applied Sciences

## 1. Project Overview

This project evaluates whether machine learning models can predict weekly retail sales one week ahead. The aim is to understand whether forecasts can support business decisions about inventory, purchasing, replenishment, staffing, and distribution.

The target variable is `Weekly_Sales`. Predictions are made for each Store–Department combination.

## 2. Business Problem

Retail sales forecasts can help businesses plan stock and staffing.

- Over-forecasting may lead to extra stock and higher holding costs.
- Under-forecasting may cause stock shortages and lost sales.

The project compares three forecasting approaches to see whether more advanced models perform better than a simple baseline.

## 3. Dataset

The project uses these supplied Walmart Store Sales Forecasting files:

- `data/train.csv` — weekly sales and related information.
- `data/features.csv` — calendar, holiday, and economic features.
- `data/stores.csv` — store metadata.

The training data contains 421,570 rows, 45 stores, and 81 departments. The date range is **5 February 2010 to 26 October 2012**.

The feature and store data are joined to the training data using the relevant store and date fields. Missing Markdown values are set to zero. Calendar features such as year, month, week, and quarter are created.

## 4. Feature Engineering

The analysis creates features separately within each Store–Department sales series:

- Previous-week sales (`lag_1`)
- Sales from two weeks earlier (`lag_2`)
- Sales from four weeks earlier (`lag_4`)
- Sales from 52 weeks earlier (`lag_52`)
- Four-week rolling mean
- Four-week rolling standard deviation
- Calendar and holiday information

The rolling features are shifted by one week to help avoid using the current week's target when creating predictors. Rows without the required lag or rolling values are removed.

## 5. Models Compared

### Naive baseline

Uses the previous week's sales for the same Store–Department combination as the forecast. This provides a simple benchmark.

### Linear Regression

Uses calendar features and historical sales features to estimate weekly sales.

### XGBoost

Uses a boosted-tree model to capture relationships that may be more complex than those captured by Linear Regression.

## 6. Testing Method

The models are evaluated using chronological walk-forward validation rather than a random train/test split.

- Six chronological test folds are used.
- Each test fold contains ten weekly dates.
- There is a one-date gap between the training period and test period.
- Each model is evaluated using the same test dates and metrics.
- This is a rolling one-step-ahead evaluation: forecasts for a test week can use previously observed actual sales.

This test setup does **not** evaluate producing all ten weeks of forecasts at once without receiving new actual sales.

## 7. Evaluation Metrics

- **MAE (Mean Absolute Error):** the average absolute difference between actual and predicted sales. Lower is better.
- **RMSE (Root Mean Squared Error):** gives more weight to large prediction errors. Lower is better.
- **MASE (Mean Absolute Scaled Error):** compares model error with a naive forecasting scale. A value below 1 indicates performance better than the naive benchmark under the report's evaluation.

Results are summarized using the mean and sample standard deviation across the six folds.

## 8. Results

The report gives the following average results across six chronological folds:

| Model | Mean MAE | Mean RMSE | Mean MASE |
|---|---:|---:|---:|
| Linear Regression | 1,792.09 | 4,160.69 | 0.911 |
| Naive | 2,213.96 | 5,866.60 | 1.150 |
| XGBoost | 1,703.68 | 4,249.13 | 0.877 |

### Main findings

- **XGBoost had the lowest mean MAE and mean MASE.**
- XGBoost's mean MAE was about 23% lower than the Naive model's mean MAE.
- **Linear Regression had the lowest mean RMSE**, suggesting it performed better on a metric that penalizes large errors.
- Both trained models performed better than the Naive baseline on average, but performance varied between test folds.

The sales trend and forecast-versus-actual charts in the report show overall patterns. Aggregate charts can hide errors in individual Store–Department combinations.

## 9. Recommendation

The report recommends testing XGBoost alongside the Naive baseline in a controlled pilot. Linear Regression can also be considered because it had the lowest mean RMSE.

The current ordering system should not be changed immediately based only on this experiment. Before using forecasts for real decisions, the business should:

- Run the Naive and XGBoost forecasts alongside the current process.
- Monitor MAE, RMSE, and MASE every week.
- Review results by store and department.
- Check stock shortages, excess stock, and staffing outcomes.
- Pay special attention to holiday weeks and departments with sudden sales increases.
- Retrain and evaluate the models when new sales data becomes available.

## 10. Limitations

- Historical data may not represent current sales or customer needs.
- The models predict one week ahead; longer-term forecasts may be less accurate.
- One model is used across stores and departments, although their sales patterns may differ.
- The evaluation covers only six test periods, so future performance may differ.
- The data may not include all relevant information about promotions, stock availability, local events, or store operations.
- Accurate forecasts do not automatically guarantee higher profit or better service; the models need to be tested in real business conditions.

## 11. Project Files

The project uses these input files:

```text
project/
├── data/
│   ├── train.csv
│   ├── features.csv
│   └── stores.csv
├── notebook.ipynb
├── results_per_fold.csv
├── summary_mean_std.csv
├── report_woldmart_salesforcust.pdf
└── README.md
```

Your actual notebook and report filenames may differ. Update this example to match the names in your repository.

- `results_per_fold.csv` contains the evaluation metrics for each model and fold.
- `summary_mean_std.csv` contains the summary statistics.
- The PDF report presents the business problem, testing method, results, charts, recommendation, and limitations.

## 12. How to Run the Project

1. Clone or download the repository.
2. Make sure the three CSV files are inside the `data/` folder.
3. Open the notebook in Jupyter Notebook or Visual Studio Code.
4. Install the Python libraries used by the notebook if they are not already installed.
5. Run the notebook from top to bottom using a clean kernel.
6. Check that the results contain **six folds and 18 rows**: six folds multiplied by three models.
7. Review the generated CSV files and compare them with the report.

If a required data file is missing, check the file name and folder path before running the analysis.

## 13. Reproducibility Note

The report uses six chronological folds, ten test dates per fold, a one-date gap, the `Weekly_Sales` target, the Naive baseline, Linear Regression, and XGBoost. Summary statistics are calculated across the six folds.

The results can change if the input data, feature engineering, fold design, model settings, or evaluation code changes. Always regenerate the result tables and charts after changing the analysis.

---

*This project was prepared as part of Big Data Analysis studies at Arcada University of Applied Sciences.*
