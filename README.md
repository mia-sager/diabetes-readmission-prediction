# Diabetes 30-Day Hospital Readmission Prediction

## Overview

This project predicts whether a patient with diabetes will be readmitted to the hospital within **30 days of discharge** using demographic, clinical, diagnostic, medication, and prior healthcare utilization data.

The analysis compares three classification approaches:

- Logistic Regression
- LASSO Logistic Regression
- Decision Tree

Because 30-day readmissions are relatively uncommon, the project emphasizes **ROC-AUC** rather than accuracy alone and also evaluates how changing the classification threshold affects recall and misclassification cost.

The project was completed for **QTM 347 at Emory University: Machine Learning**.

---

## Research Goal

The main goals of the project were to:

1. Predict 30-day hospital readmission among patients with diabetes.
2. Identify which patient and hospital-related characteristics are most associated with readmission risk.
3. Compare multiple classification models using out-of-sample performance.
4. Evaluate whether a cost-sensitive probability threshold improves decision-making when false negatives are considered more costly than false positives.

The analysis was motivated by the practical importance of identifying patients who may need additional follow-up or discharge support after hospitalization. 

---

## Data

The dataset includes approximately **101,766 hospital encounters** involving patients with diabetes across **130 U.S. hospitals between 1999 and 2008**.

Each observation represents a hospital admission and includes variables related to:

- Patient demographics
- Prior healthcare utilization
- Hospital stay characteristics
- Laboratory results
- Diagnoses
- Diabetes medications
- Admission source and type
- Discharge disposition

Approximately **11% of encounters resulted in a readmission within 30 days**, creating a substantially imbalanced classification problem. 

Additional information about the dataset and preprocessing is available in the [`data/`](data/) folder.

---

## Data Preparation

The original readmission outcome contains three categories:

- `<30` — readmitted within 30 days
- `>30` — readmitted after 30 days
- `NO` — not readmitted

For modeling, the outcome was converted to a binary target:

- `1` = readmitted within 30 days
- `0` = not readmitted within 30 days

The preprocessing workflow included:

- Removing patient and encounter identifiers
- Handling missing laboratory-result values
- One-hot encoding categorical variables
- Standardizing predictors for logistic regression models
- Splitting the data into:
  - **60% training**
  - **20% testing**
  - **20% validation**

After categorical encoding, the modeling dataset contained more than 100 predictors. 

---

## Methods

### Logistic Regression

A standard logistic regression model was used to estimate the probability of a patient being readmitted within 30 days.

### LASSO Logistic Regression

LASSO logistic regression applies an L1 penalty that shrinks weaker coefficients toward zero.

This approach was particularly useful because the one-hot encoded dataset contained a large number of predictors and LASSO provided both:

- Predictive modeling
- Feature selection and interpretability

### Decision Tree

A classification tree was also trained to predict readmission using a sequence of decision rules.

The tree provides a more interpretable nonlinear modeling approach but produced slightly weaker discrimination than the regression-based models.

---

## Model Performance

The models were compared using **ROC-AUC**, which measures how well each model distinguishes between patients who are and are not readmitted within 30 days.

| Model | Test ROC-AUC |
|---|---:|
| LASSO Logistic Regression | **0.6506** |
| Logistic Regression | 0.6506 |
| Decision Tree | 0.6390 |

LASSO achieved the strongest test-set performance, although standard logistic regression performed nearly identically.

LASSO was selected as the final model because it combined competitive predictive performance with automatic feature selection and improved interpretability. 

![Model AUC Comparison](figures/model_auc_comparison.png)

---

## Key Predictors

The LASSO model identified several variables that were strongly associated with predicted 30-day readmission risk.

Important predictors included:

- Number of prior inpatient visits
- Patient age group
- Discharge disposition
- Diagnosis categories
- Medication patterns

Prior inpatient utilization was among the strongest positive predictors, suggesting that patients with a history of repeated hospitalizations were also more likely to return within 30 days. 

![Top LASSO Predictors](figures/lasso_top_predictors.png)

---

## Class Imbalance

Only about **11% of hospital encounters** resulted in a readmission within 30 days.

This means that a model could achieve high overall accuracy simply by predicting that most patients would not be readmitted. For this reason, the project emphasizes ROC-AUC, recall, and classification costs rather than relying on accuracy alone. 

![Readmission Distribution](figures/readmission_distribution.png)

---

## Cost-Sensitive Classification

The project also examined how the probability threshold used to classify a patient as high-risk affects model performance.

A standard threshold of:

**0.50**

was compared with a cost-sensitive threshold of approximately:

**0.33**

The lower threshold was based on the assumption that a **false negative was twice as costly as a false positive**.

Lowering the threshold substantially increased recall for patients who were actually readmitted. In the submitted validation analysis, recall increased to approximately **0.95** at the lower threshold. 

However, the lower threshold also generated a large number of false positives.

The resulting weighted costs were:

| Threshold | Weighted Cost |
|---|---:|
| 0.50 | **8,282** |
| 0.33 | 15,857 |

Therefore, despite the theoretical motivation for the lower threshold, the standard 0.50 threshold produced a lower observed misclassification cost on the validation data. 

This demonstrates the importance of testing business or clinical decision rules empirically rather than relying only on theoretical thresholds.

---

## Key Findings

- LASSO logistic regression produced the strongest overall predictive performance.
- Logistic regression performed almost identically to LASSO.
- The decision tree performed slightly worse but still provided useful nonlinear classification.
- Prior inpatient visits and patient age were among the strongest predictors of readmission.
- Overall model discrimination was modest, with ROC-AUC values around **0.65**.
- Lowering the classification threshold greatly improved recall but generated substantially more false positives.
- Under the assumed 2:1 cost structure, the standard 0.50 threshold ultimately produced a lower observed validation cost.

---

## Repository Structure

```text
diabetes-readmission-prediction/
│
├── README.md
├── requirements.txt
│
├── data/
│   ├── README.md
│   └── readmission.csv
│
├── notebooks/
│   └── diabetes_readmission_prediction.ipynb
│
├── figures/
│   ├── README.md
│   ├── readmission_distribution.png
│   ├── age_distribution.png
│   ├── model_auc_comparison.png
│   ├── lasso_top_predictors.png
│   └── threshold_cost_comparison.png
│
└── report/
    └── diabetes_readmission_report.pdf
```

---

## Running the Analysis

The complete analysis is available in:

[`notebooks/diabetes_readmission_prediction.ipynb`](notebooks/diabetes_readmission_prediction.ipynb)

The notebook uses the processed datasets stored in the [`data/`](data/) directory.

To install the required Python packages:

```bash
pip install -r requirements.txt
```
---

## Limitations

Several limitations should be considered when interpreting the results.
- The dataset is highly imbalanced, making accurate identification of readmissions difficult.
- Model discrimination is modest rather than strong.
- The data cover **1999–2008**, so hospital practices, medications, and healthcare policies may differ from current conditions.
- The dataset does not include potentially important information such as social determinants of health or unstructured physician notes.
- The 2:1 false-negative-to-false-positive cost ratio is a simplified assumption and may not represent actual hospital costs.
- The analysis is observational and predictive, so relationships between predictors and readmission should not be interpreted as causal.

---

## Full Report

The full research paper, including methodology, results, references, and additional figures, is available in the [`report/`](report/) folder.
