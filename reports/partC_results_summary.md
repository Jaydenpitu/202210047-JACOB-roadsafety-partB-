# Part C Results Summary

## Student Information

**Student Name:** Jamaicah JACOB
**Student ID:** 202210047
**Part A Group:** Group 3
**Assigned Dataset Version:** Sample3_ZJ
**Assigned Member Number:** 3

## 1. Dataset Summary

* **Training rows:** 8,000
* **Test rows:** 2,000
* **Predictors:** 20
* **Target:** `is_severe_collision`

### Training Target Distribution

| Class | Meaning    | Count | Percentage |
| ----- | ---------- | ----: | ---------: |
| 0     | Non-Severe | 6,013 |     75.16% |
| 1     | Severe     | 1,987 |     24.84% |

The target is imbalanced, with severe collisions being the minority class.

## 2. Individual Investigation

**Member:** Jamaicah JACOB
**Member Number:** 3

The investigation focused on predicting **severe road collisions (Class 1)** using four assigned classification models.

## 3. Feature Groups

**Numerical:** longitude, latitude, number of vehicles, number of casualties, speed limit, casualties per vehicle.

**Ordinal:** first road class ordinal, second road class ordinal.

**Nominal:** day of week, road type, junction detail, junction control, light conditions, weather conditions, road surface conditions, urban/rural area, month, time period.

**Binary:** has second road, second road unknown.

## 4. Preprocessing

A `ColumnTransformer` inside a `Pipeline` was used.

* Numerical: median imputation + StandardScaler
* Ordinal: median imputation + StandardScaler
* Nominal: most-frequent imputation + OneHotEncoder
* Binary: passed through unchanged
* Unknown categories were ignored during one-hot encoding.

This kept preprocessing inside the modelling pipeline and helped prevent data leakage.

## 5. Candidate Models and Best Hyperparameters

| Model               | Best Hyperparameters                                                                 |
| ------------------- | ------------------------------------------------------------------------------------ |
| Logistic Regression | `C=0.1`, `class_weight='balanced'`                                                   |
| Decision Tree       | `max_depth=5`, `min_samples_leaf=1`, `class_weight='balanced'`                       |
| MLP Neural Network  | `hidden_layer_sizes=(50,)`, `alpha=0.0001`, `learning_rate_init=0.01`                |
| Random Forest       | `n_estimators=200`, `max_depth=10`, `min_samples_leaf=10`, `class_weight='balanced'` |

## 6. Validation Strategy

The **primary metric was Class 1 F1-score** because severe collisions were the minority class and both precision and recall were important.

Validation used **5-fold Stratified Cross-Validation** with:

* `n_splits=5`
* `shuffle=True`
* `random_state=42`

Accuracy, precision, recall, and ROC-AUC were used as supporting metrics.

## 7. Final Model Comparison

| Model               | Mean CV F1 | F1 Std | Accuracy | Precision | Recall | ROC-AUC |
| ------------------- | ---------: | -----: | -------: | --------: | -----: | ------: |
| Dummy Baseline      |     0.0000 |      — |   0.7516 |    0.0000 | 0.0000 |  0.5000 |
| Logistic Regression |     0.4149 | 0.0131 |   0.5970 |    0.3247 | 0.5747 |  0.6255 |
| Decision Tree       |     0.3964 | 0.0169 |   0.5900 |    0.3128 | 0.5441 |  0.6105 |
| MLP Neural Network  |     0.2902 | 0.0155 |   0.6352 |    0.2807 | 0.3004 |  0.5360 |
| Random Forest       |     0.4109 | 0.0082 |   0.6421 |    0.3475 | 0.5028 |  0.6376 |

## 8. Selected Model

**Selected model: Logistic Regression**

Logistic Regression was selected because it achieved a mean CV Class 1 F1-score of **0.4149** and had a small training-to-CV F1 gap of **0.0161**. It also provides a relatively simple and interpretable model while achieving a Class 1 recall of **0.5747** during cross-validation.

**Frozen settings:** `C=0.1`, `class_weight='balanced'`, `max_iter=1000`, `random_state=42`, threshold `0.5`.

## 9. Untouched Test-Set Results

The frozen model was refitted using all 8,000 training rows before evaluating the untouched 2,000-row test set.

| Metric              |                      Test Result |
| ------------------- | -------------------------------: |
| Accuracy            |                           0.6005 |
| Precision (Class 1) |                           0.3322 |
| Recall (Class 1)    |                           0.6016 |
| F1-score (Class 1)  |                           0.4254 |
| ROC-AUC             | [Enter actual Q8.3 test ROC-AUC] |

### Confusion Matrix

|                       | Predicted Non-Severe | Predicted Severe |
| --------------------- | -------------------: | ---------------: |
| **Actual Non-Severe** |                  902 |              601 |
| **Actual Severe**     |                  198 |              299 |

**TN = 902, FP = 601, FN = 198, TP = 299**

The test F1-score of **0.4254** was close to the mean CV F1-score of **0.4149**, with a difference of approximately **0.0105**.

## 10. Recurring Error Patterns

The test set produced **799 misclassified collisions**.

Two recurring patterns identified from the actual misclassified records were:

1. **[Insert actual recurring pattern from Task 8.6]**
2. **[Insert actual recurring pattern from Task 8.6]**

These patterns describe observations in this test sample and should not be treated as general conclusions without further testing.

## 11. Important Subgroup Finding

Performance differed between the two `urban_or_rural_area` groups.

| Group |  Size | Class 1 Prevalence | Recall | False-Negative Rate |
| ----- | ----: | -----------------: | -----: | ------------------: |
| 1     | 1,350 |             21.78% | 49.32% |              50.68% |
| 2     |   650 |             31.23% | 75.86% |              24.14% |

Group 2 had a higher severe-collision prevalence and higher Class 1 recall than Group 1.

## 12. Fairness / Proxy Concern

Location-related features such as `latitude`, `longitude`, and `urban_or_rural_area` may act as proxies for differences in road and traffic conditions.

The difference in subgroup performance does **not by itself prove discrimination**. More representative data and testing across different locations would be needed to assess fairness and generalisability.

## 13. Limitations

1. The model has relatively high false-positive and false-negative rates, so some collisions are incorrectly classified.
2. The model was trained and tested on one dataset, so its performance may not generalise to other regions or road conditions.

## 14. Recommended Use

The model can be used as a **supporting tool for road-safety planning**, such as identifying collision patterns and areas for further investigation.

## 15. Inappropriate Use

The model should **not be used as the only basis for important road-safety decisions**, because some predictions are incorrect and performance may vary across different data.

## 16. Important Finding for Part C Comparison

An important finding to compare across dataset versions is the **stability of severe-collision prediction performance**, especially the Class 1 F1-score, recall, false-negative rate, and model generalisation.

Part C should compare **patterns, stability, errors, subgroup behaviour, and conclusions** across samples rather than treating small differences in model scores as direct proof that one model is superior.

