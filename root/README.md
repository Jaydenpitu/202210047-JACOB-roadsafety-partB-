# Road Safety – Part B Predictive Modelling

## Student Information

**Student Name:** Jamaicah JACOB
**Student ID:** 202210047
**Project:** Road Safety – Part B Predictive Modelling
**Part A Group:** [Enter Part A group]
**Assigned Dataset Version:** [Enter dataset version]
**Assigned Member Number:** [Enter member number]

## 1. Project Overview

This project develops machine learning classification models to predict whether a road collision is **severe** or **non-severe**.

The target variable is:

`is_severe_collision`

* **0:** Non-Severe / Slight Collision
* **1:** Severe Collision

The main focus was predicting the minority **Class 1 (Severe Collision)** while considering class imbalance, model performance, errors, and subgroup behaviour.

## 2. Dataset

The dataset was divided into training and holdout test data.

* **Training rows:** 8,000
* **Test rows:** 2,000
* **Predictor columns:** 20

### Training Target Distribution

| Class | Description | Count | Percentage |
| ----- | ----------- | ----: | ---------: |
| 0     | Non-Severe  | 6,013 |     75.16% |
| 1     | Severe      | 1,987 |     24.84% |

The target is imbalanced, with Class 1 representing approximately 24.84% of the training data.

## 3. Feature Groups

The 20 predictor variables were grouped as follows.

### Numerical Features

* longitude
* latitude
* number_of_vehicles
* number_of_casualties
* speed_limit
* casualties_per_vehicle

### Ordinal Features

* first_road_class_ordinal
* second_road_class_ordinal

### Nominal Features

* day_of_week
* road_type
* junction_detail
* junction_control
* light_conditions
* weather_conditions
* road_surface_conditions
* urban_or_rural_area
* month
* time_period

### Binary Features

* has_second_road
* second_road_unknown

## 4. Preprocessing

A `ColumnTransformer` inside a Scikit-learn `Pipeline` was used.

* Numerical features: median imputation and standardisation.
* Ordinal features: median imputation and standardisation.
* Nominal features: most-frequent imputation and one-hot encoding.
* Binary features: passed through unchanged.
* Unknown categories were handled using `handle_unknown='ignore'`.

Keeping preprocessing inside the pipeline helped prevent data leakage during cross-validation and testing.

## 5. Machine Learning Models

Four assigned classification models were evaluated:

1. Logistic Regression
2. Decision Tree
3. MLP Neural Network
4. Random Forest

A majority-class `DummyClassifier` was also used as the baseline.

## 6. Evaluation Strategy

The primary evaluation metric was **Class 1 F1-score** because severe collisions were the minority class and both precision and recall were important.

The models were evaluated using **5-fold Stratified Cross-Validation** with:

* `n_splits=5`
* `shuffle=True`
* `random_state=42`

Accuracy, precision, recall, and ROC-AUC were also recorded as supporting metrics.

## 7. Cross-Validation Results

| Model               | Mean CV F1 | F1 Std | Accuracy | Precision | Recall | ROC-AUC |
| ------------------- | ---------: | -----: | -------: | --------: | -----: | ------: |
| Dummy Baseline      |     0.0000 |      — |   0.7516 |    0.0000 | 0.0000 |  0.5000 |
| Logistic Regression |     0.4149 | 0.0131 |   0.5970 |    0.3247 | 0.5747 |  0.6255 |
| Decision Tree       |     0.3964 | 0.0169 |   0.5900 |    0.3128 | 0.5441 |  0.6105 |
| MLP Neural Network  |     0.2902 | 0.0155 |   0.6352 |    0.2807 | 0.3004 |  0.5360 |
| Random Forest       |     0.4109 | 0.0082 |   0.6421 |    0.3475 | 0.5028 |  0.6376 |

## 8. Final Model

The final frozen model was **Logistic Regression**.

### Hyperparameters

* `C = 0.1`
* `class_weight = 'balanced'`
* `max_iter = 1000`
* `random_state = 42`
* Decision threshold = `0.5`

The model was selected using development data before the holdout test set was evaluated.

## 9. Holdout Test Results

The final model was refitted using all 8,000 training observations and then evaluated once on the untouched 2,000-row test set.

| Metric              |                 Test Result |
| ------------------- | --------------------------: |
| Accuracy            |                      0.6005 |
| Precision (Class 1) |                      0.3322 |
| Recall (Class 1)    |                      0.6016 |
| F1-score (Class 1)  |                      0.4254 |
| ROC-AUC             | [Enter actual test ROC-AUC] |

### Confusion Matrix

|                       | Predicted Non-Severe | Predicted Severe |
| --------------------- | -------------------: | ---------------: |
| **Actual Non-Severe** |                  902 |              601 |
| **Actual Severe**     |                  198 |              299 |

* **True Negative:** 902
* **False Positive:** 601
* **False Negative:** 198
* **True Positive:** 299

The test F1-score of **0.4254** was close to the mean CV F1-score of **0.4149**.

## 10. Error Analysis

The model produced:

* **601 false positives**
* **198 false negatives**
* **799 total misclassified collisions**

A false positive means a non-severe collision was predicted as severe.

A false negative means a severe collision was predicted as non-severe.

The false-negative rate was approximately **39.84%**, while the false-positive rate was approximately **39.99%**.

Two recurring error patterns identified from the misclassified test records should be reported in the Part C results summary.

## 11. Subgroup Analysis

Performance was also examined using `urban_or_rural_area`.

| Group |  Size | Class 1 Prevalence | Recall | False-Negative Rate |
| ----- | ----: | -----------------: | -----: | ------------------: |
| 1     | 1,350 |             21.78% | 49.32% |              50.68% |
| 2     |   650 |             31.23% | 75.86% |              24.14% |

The results show different Class 1 recall and false-negative rates between the two groups.

Location-related variables such as latitude, longitude, and urban/rural area may act as proxies for geographical differences in road and traffic conditions. The subgroup performance difference alone does not prove discrimination.

## 12. Limitations

1. The model has relatively high false-positive and false-negative rates, meaning some collisions are incorrectly classified.
2. The model was trained and tested on one dataset, so its performance may not generalise to other regions or road conditions.

## 13. Appropriate and Inappropriate Use

### Appropriate Use

The model can be used as a **supporting tool for road-safety planning**, such as identifying collision patterns and areas for further investigation.

### Inappropriate Use

The model should **not be used as the only basis for important road-safety decisions**, because some predictions are incorrect and performance may vary across different data and subgroups.

## 14. Repository Structure

```text
202210047-Jacob-roadsafety-partB/
│
├── data/
│   └── processed/
│       ├── X_train.csv
│       ├── X_test.csv
│       ├── y_train.csv
│       └── y_test.csv
│
├── notebooks/
│   └── 02_partB_predictive_modelling.ipynb
│
├── reports/
│   └── partC_results_summary.md
│
├── figures/
│   └── confusion_matrix.png
│
├── AI_USE_LOG.md
│
└── README.md
```

## 15. Part C Comparison

The main finding to compare across dataset versions is the **stability of severe-collision prediction performance**, especially Class 1 F1-score, recall, false-negative rate, errors, and subgroup behaviour.

Small differences in model scores across different samples should not be treated as direct proof that one model is superior. The Part C comparison should focus on broader patterns, stability, errors, subgroup behaviour, and whether the main conclusions remain consistent across dataset versions.

## 16. AI Usage

ChatGPT was used as a support tool for understanding assignment requirements, checking Python code, debugging, interpreting results, and preparing documentation.

The detailed record of AI use is provided in:

`AI_USE_LOG.md`

## 17. Main Files

* **Predictive modelling notebook:** `notebooks/02_partB_predictive_modelling.ipynb`
* **Part C results summary:** `reports/partC_results_summary.md`
* **AI usage log:** `AI_USE_LOG.md`
* **Confusion matrix:** `figures/confusion_matrix.png`

