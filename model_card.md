# Model Card: D2C Churn Prediction (60-Day)

## 1. Intended Use
* **Primary Objective:** To predict whether an existing customer of a D2C personal-care brand will churn within the next 60 days.
* **Intended Users:** Marketing and Customer Success teams, utilizing the predictions to trigger targeted retention campaigns (e.g., win-back discounts, VIP outreach) prior to actual churn.

## 2. Data Used
* **Source:** Internal CRM and web analytics database (`rfm_modeling_snapshot.csv`).
* **Temporal Boundaries:** All features are strictly bounded by the snapshot date of `2025-09-30` to prevent data leakage. Target window is 60 days post-snapshot.
* **Data Split:** Stratified split maintaining a ~47% churn baseline.
    * Training Set: 1,679 records
    * Validation Set: 361 records
    * Testing Set: 360 records

## 3. Model Approach
* **Algorithm:** XGBoost Classifier. Selected for its robust handling of tabular data, non-linear feature relationships, and built-in handling of varying feature scales.
* **Baseline Comparison:** A Logistic Regression model was used as a baseline.
* **Feature Selection:** 
    * Excluded: `customer_id`, `snapshot_date` (Leakage prevention).
    * Categorical Features: Handled via One-Hot Encoding.
    * Numerical Features: Standardized using `StandardScaler`.

## 4. Performance Metrics (Test Set)
The model's decision threshold was manually tuned from the default `0.50` down to `0.40` to prioritize Recall (catching more churners at the expense of slight over-flagging).
* **Accuracy:** 78%
* **Precision (Churn):** 73% (When the model predicts churn, it is correct 73% of the time).
* **Recall (Churn):** 82% (The model successfully identifies 82% of all actual churners).
* **F1-Score (Churn):** 77%

**Top Driving Features:**
1. `recency_days` (Score: 0.1255)
2. `acquisition_channel_Organic` (Score: 0.0487)
3. `return_rate_180d` (Score: 0.0424)

## 5. Limitations & Ethical Risks
* **Limitations:** The model is trained on a single, static snapshot (`2025-09-30`). It does not account for macro-seasonality (e.g., holiday shopping spikes). It also suffers from the "cold start" problem and cannot accurately predict churn for brand-new customers with no RFM history.
* **Ethical Risks:** Target allocation. If the model biases against organic traffic, it may result in unequal distribution of loyalty rewards or discounts to specific demographics.

## 6. Monitoring & Deprecation
* **Monitoring Needs:** The distribution of `recency_days` and conversion rates should be monitored monthly for data drift. 
* **When not to use:** This model should not be used for inventory forecasting or supply chain management. It should be retrained immediately if the brand launches a subscription-based product tier, as the fundamental definition of "churn" would change.
