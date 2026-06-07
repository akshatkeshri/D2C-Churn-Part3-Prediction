# Model Error Analysis & Business Impact

## 1. False Positive Analysis (Predicted Churn, Actually Retained)
A False Positive occurs when the XGBoost model flags a customer as "At-Risk," but the customer naturally makes another purchase within 60 days without intervention.

* **Business Risk:** Margin Erosion. If marketing blindly trusts the model and sends a 20% discount code to these users, the business loses margin on a sale that was already going to happen. It trains loyal customers to wait for discounts.
* **Mitigation:** Lower-cost retention actions (e.g., "early access" emails instead of heavy discounts) should be used for edge-case predictions.

## 2. False Negative Analysis (Predicted Retained, Actually Churned)
A False Negative occurs when the model thinks a customer is safe, but they silently abandon the brand. 

* **Business Risk:** Revenue Leakage & High CAC. Losing a proven customer means the business must spend high Customer Acquisition Costs (CAC) to replace them. This is significantly more damaging to the long-term LTV/CAC ratio than a False Positive.
* **Mitigation:** We lowered the model's decision threshold to `0.40` specifically to reduce False Negatives. Our Recall is 82%, meaning we only miss 18% of churning users.

## 3. Specific Customer Examples (Manual Review)

*Note: The following 10 test-set cases highlight the boundaries of the model's predictive power.*

| Customer ID | Actual Outcome | Model Prediction | Error Type | Interpretation |
| :--- | :--- | :--- | :--- | :--- |
| **CUST00189** | Retained (0) | Churned (1) | False Positive | Likely triggered by high `recency_days`, but customer eventually bought. |
| **CUST01203** | Retained (0) | Churned (1) | False Positive | Model over-indexed on an abandoned cart. |
| **CUST00940** | Retained (0) | Churned (1) | False Positive | Likely penalized for organic acquisition channel. |
| **CUST01029** | Retained (0) | Churned (1) | False Positive | Borderline probability score (just over 0.40). |
| **CUST01652** | Retained (0) | Churned (1) | False Positive | Model struggled with this user's irregular purchase frequency. |
| **CUST00616** | Churned (1) | Retained (0) | False Negative | Model was fooled by recent web session activity. |
| **CUST00309** | Churned (1) | Retained (0) | False Negative | High historical spend masked sudden drop in intent. |
| **CUST01725** | Churned (1) | Retained (0) | False Negative | Model missed a silent drop-off not captured by support tickets. |
| **CUST01579** | Churned (1) | Retained (0) | False Negative | User likely churned due to an external factor (e.g., competitor pricing) not in our DB. |
| **CUST02248**| Churned (1) | Retained (0) | False Negative | Borderline probability score (just under 0.40). |
