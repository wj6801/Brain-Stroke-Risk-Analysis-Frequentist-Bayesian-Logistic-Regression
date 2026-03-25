# Brain Stroke Risk Analysis: A Comparative Study Using Frequentist and Bayesian Logistic Regression

**Author:** Wonjae Lee  
**Affiliation:** University of California, Irvine  
**Date:** March 2026  
**Statistical Methods:** Linear Probability Model, Frequentist Logistic Regression, Bayesian Logistic Regression, Lasso Regularization

---

## Abstract

This project analyzes the Brain Stroke Dataset, a patient-level dataset containing demographic, lifestyle, and medical variables associated with stroke risk. The response variable is `stroke`, a binary indicator of whether an individual has experienced a stroke. The main objective of this study is both explanatory and predictive: to identify the factors most strongly associated with stroke occurrence and to compare the performance of several regression-based models for predicting stroke risk.

Because the outcome variable is binary, the analysis focuses primarily on a frequentist logistic regression model and a Bayesian logistic regression model, while a linear probability model is included as a simple baseline for comparison. Exploratory data analysis is used to examine the distribution of predictors and their relationship with stroke, and special attention is given to the class imbalance in the dataset, since stroke cases are relatively rare. Model evaluation is based on metrics more appropriate for imbalanced classification problems, including ROC-AUC, precision, recall, and F1 score.

The results show that several health-related and demographic variables, particularly age and cardiovascular risk factors such as hypertension and heart disease, are important predictors of stroke. Among the models considered, logistic-regression-based approaches provide a better balance of interpretability and statistical validity than the linear probability model. Overall, this project finds that logistic regression is the most appropriate framework for modeling stroke occurrence in this dataset, while the Bayesian approach provides additional uncertainty quantification that complements the frequentist analysis.

---

## Introduction

Stroke is a major health concern because it can lead to death, long-term disability, and substantial reductions in quality of life. As a result, identifying factors associated with stroke risk is an important problem in both medicine and public health. Statistical modeling can help improve understanding of stroke-related risk factors and provide tools for estimating the probability of stroke occurrence based on patient characteristics.

This project analyzes the Brain Stroke Dataset, which contains patient-level demographic, lifestyle, and medical variables related to stroke risk. The dataset includes predictors such as age, hypertension, heart disease, glucose level, BMI, smoking status, and other background characteristics, with `stroke` as the binary response variable indicating whether a patient has experienced a stroke. Also, there were no missing values in the data.

The purpose of this analysis is both explanatory and predictive: to identify which factors are most strongly associated with stroke and to compare the performance of several regression-based models for predicting stroke occurrence. Since the outcome is binary, the main focus is on frequentist logistic regression and Bayesian logistic regression, while a linear probability model is included as a simple baseline for comparison.

Because stroke cases are relatively rare in the data, this project also pays careful attention to class imbalance and uses evaluation metrics beyond simple accuracy, including ROC-AUC, precision, recall, and F1 score. Through exploratory data analysis, model fitting, and model comparison, the final goal is to determine which method provides the best balance of interpretability, statistical validity, and predictive performance for this dataset.

---

## Literature Review

Stroke is a major public-health concern because it is both a leading cause of death and a major cause of long-term disability. In the United States, the CDC describes stroke as a leading cause of death and a major cause of serious disability in adults, while the WHO identifies stroke as an ongoing global health challenge requiring stronger prevention, treatment, and rehabilitation systems.

Prior literature has identified both non-modifiable and modifiable stroke risk factors. Boehme, Esenwa, and Elkind classify age, sex, race/ethnicity, and genetics as non-modifiable factors, and they note that stroke is strongly associated with aging, with incidence increasing substantially at older ages. The same review also highlights several modifiable risk factors, including hypertension, smoking, diet, and physical inactivity.

Large-scale global evidence also supports the importance of cardiovascular and metabolic risk factors in stroke burden. The GBD 2021 Stroke Risk Factor Collaborators report that stroke was the third most common cause of death worldwide in 2021 and show that the burden of stroke remains strongly linked to major risk factors over time. This broader evidence supports the relevance of variables such as hypertension, heart disease, glucose-related measures, smoking behavior, and body-composition indicators when studying stroke occurrence.

This background helps justify the use of the Brain Stroke Dataset for the present project. The dataset includes demographic, lifestyle, and health-related variables such as age, hypertension, heart disease, average glucose level, BMI, and smoking status, all of which are plausible predictors given the established stroke-risk literature. Because the response variable in this project is binary, the main analytical focus is on regression methods designed for binary outcomes. A linear probability model is included only as a simple baseline, while frequentist logistic regression serves as the main classical model and Bayesian logistic regression extends the analysis through posterior uncertainty quantification.

---

## Descriptive Statistics

### Table 1: Descriptive Statistics of Numeric Features

|  | count | mean | std | min | 25% | 50% | 75% | max |
|---|---|---|---|---|---|---|---|---|
| age | 3984.0 | 43.6124 | 22.6290 | 0.08 | 26.0000 | 45.000 | 61.0000 | 82.00 |
| hypertension | 3984.0 | 0.0976 | 0.2969 | 0.00 | 0.0000 | 0.000 | 0.0000 | 1.00 |
| heart_disease | 3984.0 | 0.0540 | 0.2260 | 0.00 | 0.0000 | 0.000 | 0.0000 | 1.00 |
| avg_glucose_level | 3984.0 | 106.3429 | 45.3679 | 55.12 | 77.4375 | 92.155 | 114.3225 | 267.76 |
| bmi | 3984.0 | 28.5807 | 6.7598 | 14.00 | 23.9000 | 28.200 | 32.7000 | 48.90 |
| stroke | 3984.0 | 0.0497 | 0.2173 | 0.00 | 0.0000 | 0.000 | 0.0000 | 1.00 |

### Target Variable Distribution

**Figure 1** shows the class distribution of the target variable `stroke`. The dataset is heavily imbalanced — stroke cases account for roughly 5% of the training set while the remaining ~95% are non-stroke. This severe class imbalance is the most critical characteristic of this dataset. A naive classifier that always predicts "No Stroke" would achieve ~95% accuracy while being completely useless. This means accuracy alone is a misleading metric; precision, recall, F1-score, or AUC-ROC should be used for model evaluation. Techniques such as class weighting, oversampling, or undersampling may be needed during modeling.

### Categorical Feature Distributions

**Figure 2** shows the distribution of all categorical features. Gender is Female/Male. Most subjects are married and work in the private sector. Urban and rural residence types are roughly equal. The majority of subjects have never smoked or have an unknown smoking status, while active smokers are a smaller group. Critically, very few subjects have hypertension or heart disease — mirroring the rarity of stroke itself — which means these binary risk factors will be highly informative despite their low frequency.

### Numeric Feature Distributions

**Figure 3** shows distributions for the three numeric features. Age is left-skewed with a concentration of older subjects, reflecting that stroke risk increases with age. `avg_glucose_level` appears bimodal — one peak around normal fasting glucose (~80–100 mg/dL) and a second peak at higher values (~200+ mg/dL), suggesting a subgroup with diabetes or pre-diabetes, which is a known stroke risk factor. BMI follows an approximately normal distribution centered around 28–30, which is in the overweight range, consistent with a general adult population.

### Correlation Heatmap

**Figure 4** shows pairwise Pearson correlations among the numeric features and the target. Age is the strongest correlate of `stroke`, followed by `hypertension` and `avg_glucose_level` — consistent with established medical knowledge that stroke risk rises sharply with age, high blood pressure, and elevated glucose. Age also correlates moderately with `hypertension` and `heart_disease`, which is expected since these conditions become more prevalent in older populations. BMI shows weak correlations with stroke and the other features, suggesting it may be a less discriminative predictor on its own.

### Pairplot of Numeric Features by Stroke Outcome

**Figure 5** is a pairplot of the three numeric features colored by stroke outcome. The diagonal KDE plots show that stroke patients (red) tend to be older, with their age distribution shifted clearly to the right compared to non-stroke patients (blue) — making age the most visually separable feature. `avg_glucose_level` shows a similar but weaker separation, with stroke patients having a slightly higher glucose distribution. `bmi` distributions overlap heavily between the two classes, confirming it is a weak predictor. The scatter plots reinforce that the clearest separation comes from the age axis in combination with glucose level.

### Cross-Tabulations vs Stroke

**Gender:** Female stroke rate is 111/(2211+111) = 4.8%, Male is 87/(1575+87) = 5.2%. These are nearly identical, so gender genuinely isn't informative here.

**Smoking status:** Former smokers have the highest stroke rate at 58/712 = 8.1%, compared to never smoked at 70/1455 = 4.8% and current smokers at 34/620 = 5.5%. The Unknown category is 36/1197 = 3.0%. So former smokers have roughly 1.7× the stroke rate of never-smokers — that's a meaningful difference and consistent with cumulative cardiovascular damage from past smoking.

**Work type:** Self-employed shows 55/657 = 8.4%, which is the highest. Government and Private are around 4.3% and 5.2%. Children are essentially zero at 2/529 = 0.4%. But the self-employed effect is likely confounded by age — self-employed people tend to be older.

**Ever married:** This is the most visually striking one. Married individuals have a stroke rate of 176/2646 = 6.7%, while never-married is only 22/1338 = 1.6% — a fourfold difference. But again, this is almost certainly an age proxy since marriage correlates strongly with age.

### Pairwise Correlations

#### Table 2: Pairwise Pearson Correlations (Absolute Values)

| Feature A | Feature B | Pearson \|r\| |
|---|---|---|
| age | bmi | 0.376 |
| age | hypertension | 0.285 |
| age | heart_disease | 0.259 |
| age | stroke | 0.243 |
| age | avg_glucose_level | 0.234 |
| avg_glucose_level | bmi | 0.186 |
| avg_glucose_level | hypertension | 0.175 |
| bmi | hypertension | 0.156 |
| avg_glucose_level | heart_disease | 0.156 |
| hypertension | stroke | 0.150 |
| avg_glucose_level | stroke | 0.146 |
| heart_disease | stroke | 0.124 |
| hypertension | heart_disease | 0.116 |
| bmi | heart_disease | 0.065 |
| bmi | stroke | 0.063 |

---

## Part 2: Modeling

Three models are fitted to predict stroke (binary outcome, ~5% positive rate):

### Table 3: Model Summary

| Model | Family | Link | Coefficient Interpretation |
|---|---|---|---|
| Linear Probability Model | Gaussian | Identity | Change in P(stroke) per unit |
| Frequentist Logistic Regression | Bernoulli | Logit | Log odds-ratio per unit change |
| Bayesian Logistic Regression | Bernoulli | Logit | Full posterior over log-odds coefficients |

**Preprocessing:** Categorical features are one-hot encoded (drop-first); continuous features (`age`, `avg_glucose_level`, `bmi`) are standardized (z-score). The train/test split from Part 1 is reused.

### Methods

The response variable in this study is binary (stroke = 0 or 1), so the primary modeling framework is logistic regression. Three models are fitted and compared, each making different assumptions about the data-generating process.

**Linear Probability Model (LPM).** As a baseline, an ordinary least squares regression is fitted directly on the binary outcome:

$$P(\text{stroke}_i = 1 \mid x_i) = \beta_0 + \beta_1 x_{i1} + \cdots + \beta_p x_{ip}$$

This treats the probability of stroke as a linear function of the predictors. The LPM is easy to interpret — each coefficient is the change in P(stroke) per unit change in the predictor — but it can produce predicted probabilities outside [0, 1] and violates the constant-variance assumption of OLS when the outcome is binary. It is included here only as a reference point.

**Frequentist Logistic Regression.** The logistic regression model uses a logit link to constrain predicted probabilities to (0, 1):

$$\log\left(\frac{p_i}{1 - p_i}\right) = \beta_0 + \beta_1 x_{i1} + \cdots + \beta_p x_{ip}$$

where $p_i = P(\text{stroke}_i = 1 \mid x_i)$. Coefficients are estimated via maximum likelihood using `statsmodels`, which provides standard errors, z-statistics, and p-values for inference. Exponentiated coefficients are interpreted as odds ratios. To reduce the feature set, Lasso logistic regression (L1 penalty) is first applied using cross-validated regularization strength, and the 9 features with non-zero Lasso coefficients are retained for the final frequentist and Bayesian models.

**Bayesian Logistic Regression.** The same logistic model is re-estimated in a Bayesian framework using PyMC. Weakly informative Normal priors are placed on all coefficients:

$$\beta_j \sim \text{Normal}(0, 2), \quad j = 0, 1, \ldots, p$$

Posterior inference is performed via the No-U-Turn Sampler (NUTS) with 1,000 tuning steps and 1,000 posterior draws per chain across 2 chains. The posterior mean is used for point predictions, and 95% highest density intervals (HDIs) provide credible intervals for each coefficient. A prior sensitivity analysis compares results under Normal(0, 2) and Normal(0, 1) priors to check that conclusions are not driven by the prior specification. Model adequacy is assessed through posterior predictive checks (comparing replicated stroke prevalence to the observed rate) and WAIC/LOO for information-theoretic model comparison.

**Preprocessing and evaluation.** Categorical features are one-hot encoded with one reference category dropped to avoid perfect collinearity. Continuous features (age, avg_glucose_level, bmi) are standardized to z-scores so that logistic regression coefficients are on a comparable scale. The dataset is split 80/20 into training (n = 3,984) and test (n = 997) sets with stratification on the outcome. Model discrimination is evaluated using AUC-ROC, and because of the severe class imbalance (~5% stroke rate), precision-recall curves and Average Precision (AP) are reported alongside confusion matrices. Generalization is assessed via stratified 5-fold cross-validation for the frequentist models. Multicollinearity is checked using variance inflation factors (VIF).

**Training features:** (3984, 14) | **Test features:** (997, 14)

**Encoded columns:** age, hypertension, heart_disease, avg_glucose_level, bmi, gender_Male, ever_married_Yes, work_type_Private, work_type_Self-employed, work_type_children, Residence_type_Urban, smoking_status_formerly smoked, smoking_status_never smoked, smoking_status_smokes

---

### Model 1: Linear Probability Model (OLS)

**LPM (OLS) — AUC-ROC: 0.8396**

#### Table 4: LPM Classification Report

|  | precision | recall | f1-score | support |
|---|---|---|---|---|
| No Stroke | 0.95 | 1.00 | 0.97 | 947.00 |
| Stroke | 0.00 | 0.00 | 0.00 | 50.00 |
| accuracy | 0.95 | 0.95 | 0.95 | 0.95 |
| macro avg | 0.47 | 0.50 | 0.49 | 997.00 |
| weighted avg | 0.90 | 0.95 | 0.93 | 997.00 |

**Figure 7** shows the ten largest LPM coefficients by absolute magnitude. Positive values increase P(stroke); negative values decrease it. `work_type_children`, `age`, and `hypertension` are shown to be features with the highest coefficients.

---

### Model 2: Frequentist Logistic Regression

To ensure a fair, apples-to-apples comparison with the Bayesian model, both models use the same 9 Lasso-selected features: age, hypertension, heart_disease, avg_glucose_level, bmi, smoking_status_never smoked, ever_married_Yes, work_type_Self-employed, and work_type_Private. Inference is via maximum likelihood using `statsmodels`, which provides coefficient estimates, standard errors, z-scores, and p-values.

**Frequentist Logistic Regression — AUC-ROC: 0.8475**

#### Table 5: Frequentist Logistic Regression Classification Report

|  | precision | recall | f1-score | support |
|---|---|---|---|---|
| No Stroke | 0.95 | 1.00 | 0.97 | 947.00 |
| Stroke | 0.00 | 0.00 | 0.00 | 50.00 |
| accuracy | 0.95 | 0.95 | 0.95 | 0.95 |
| macro avg | 0.47 | 0.50 | 0.49 | 997.00 |
| weighted avg | 0.90 | 0.95 | 0.93 | 997.00 |

#### Table 6: Frequentist Logistic Regression Coefficients

| Feature | Coef | SE | z_score | p_value |
|---|---|---|---|---|
| age | 1.5706 | 0.1384 | 11.3471 | 0.0000 |
| hypertension | 0.5484 | 0.1794 | 3.0572 | 0.0022 |
| smoking_status_never smoked | -0.2767 | 0.1623 | -1.7049 | 0.0882 |
| work_type_Private | 0.2374 | 0.2388 | 0.9941 | 0.3202 |
| avg_glucose_level | 0.2156 | 0.0602 | 3.5845 | 0.0003 |
| heart_disease | 0.2115 | 0.2172 | 0.9739 | 0.3301 |
| ever_married_Yes | -0.1905 | 0.2509 | -0.7594 | 0.4476 |
| bmi | 0.0736 | 0.0952 | 0.7732 | 0.4394 |
| work_type_Self-employed | -0.0328 | 0.2673 | -0.1226 | 0.9024 |

**Figure 8** shows the top frequentist logistic regression coefficients by absolute magnitude. Coefficients are on the log odds-ratio scale; exponentiated, they give the multiplicative change in stroke odds per unit change in the predictor. `age`, `hypertension`, and `smoking_status_never smoked` are the top 3 highest coefficients.

#### Multicollinearity Diagnostics (VIF)

##### Table 7: Variance Inflation Factors (Full Feature Set)

| Feature | VIF |
|---|---|
| ever_married_Yes | 4.44 |
| work_type_Private | 3.36 |
| age | 2.71 |
| work_type_children | 2.40 |
| smoking_status_never smoked | 2.31 |
| Residence_type_Urban | 1.92 |
| work_type_Self-employed | 1.75 |
| smoking_status_formerly smoked | 1.75 |
| gender_Male | 1.70 |
| smoking_status_smokes | 1.64 |
| bmi | 1.37 |
| hypertension | 1.24 |
| heart_disease | 1.17 |
| avg_glucose_level | 1.11 |

All VIFs are below 5, so the collinearity doesn't undermine inference.

#### Odds Ratios and 95% Confidence Intervals

##### Table 8: Odds Ratios and 95% Confidence Intervals

| Feature | Coefficient | OR | OR_CI_lower | OR_CI_upper | p_value |
|---|---|---|---|---|---|
| age | 1.5706 | 4.8097 | 3.6669 | 6.3086 | 0.0000 |
| hypertension | 0.5484 | 1.7304 | 1.2175 | 2.4595 | 0.0022 |
| work_type_Private | 0.2374 | 1.2679 | 0.7940 | 2.0246 | 0.3202 |
| avg_glucose_level | 0.2156 | 1.2407 | 1.1027 | 1.3959 | 0.0003 |
| heart_disease | 0.2115 | 1.2355 | 0.8072 | 1.8910 | 0.3301 |
| bmi | 0.0736 | 1.0764 | 0.8931 | 1.2974 | 0.4394 |
| work_type_Self-employed | -0.0328 | 0.9678 | 0.5732 | 1.6340 | 0.9024 |
| ever_married_Yes | -0.1905 | 0.8265 | 0.5055 | 1.3515 | 0.4476 |
| smoking_status_never smoked | -0.2767 | 0.7583 | 0.5517 | 1.0423 | 0.0882 |

The top predictors by OR magnitude are `age`, `hypertension`, and `avg_glucose_level`. Age stands out with an OR of approximately 4.81, meaning each standard-deviation increase in age is associated with roughly a fivefold increase in stroke odds. Hypertension (OR ≈ 1.73) and average glucose level (OR ≈ 1.24) also show statistically significant positive associations with stroke (p < 0.05). Features whose 95% CI for the OR includes 1.0 — such as `heart_disease`, `bmi`, `work_type_Private`, `work_type_Self-employed`, and `ever_married_Yes` — are not statistically significant at α = 0.05 in this model.

#### AIC and BIC

##### Table 9: Frequentist Logistic Regression Information Criteria

| Metric | Value |
|---|---|
| AIC | 1273.78 |
| BIC | -31690.85 |

#### Interpretation of Key Predictors

**Age:** Age has the largest positive odds ratio of all predictors (OR ≈ 4.81). This is the single most powerful predictor of stroke and is consistent with decades of epidemiological evidence that stroke risk rises sharply with age (p < 0.001).

**Hypertension:** Patients with hypertension have higher odds of stroke than those without (OR ≈ 1.73, p = 0.002). This is clinically expected: chronically elevated blood pressure damages arterial walls and is a major modifiable risk factor for both ischemic and hemorrhagic stroke.

**Average Glucose Level:** Higher average glucose is associated with increased stroke odds even after adjusting for age, hypertension, and heart disease (OR ≈ 1.24, p < 0.001). Each standard deviation increase in glucose raises stroke odds by roughly 24%. This captures the contribution of diabetes and pre-diabetes, which are established stroke risk factors via both large-vessel atherosclerosis and small-vessel disease.

**Heart Disease:** Individuals with a history of heart disease show elevated stroke odds (OR ≈ 1.24), reflecting the shared cardiovascular risk pathways between cardiac conditions (e.g., atrial fibrillation, cardioembolism) and cerebrovascular events. However, this predictor does not reach statistical significance (p = 0.33) in this model, likely due to the small number of heart disease cases in the dataset.

**Smoking Status (Never Smoked):** Having never smoked is associated with a modestly lower odds of stroke (OR ≈ 0.76, p = 0.088), approaching but not reaching conventional significance. This is directionally consistent with smoking being an established cardiovascular risk factor.

---

### Model 3: Bayesian Logistic Regression

A Bayesian logistic regression is fitted using PyMC with weakly informative Normal priors (µ=0, σ=2) on all log-odds coefficients. Inference is performed via NUTS (No-U-Turn Sampler) with 1,000 posterior draws after 1,000 warm-up steps.

To ensure a fair comparison with the frequentist model, the same nine features selected by Lasso are used: age, hypertension, heart_disease, avg_glucose_level, bmi, smoking_status_never smoked, ever_married_Yes, work_type_Self-employed, and work_type_Private. The posterior mean is used for point predictions on the test set.

**Bayesian Logistic Regression — AUC-ROC: 0.8479**

#### Table 10: Bayesian Logistic Regression Classification Report

|  | precision | recall | f1-score | support |
|---|---|---|---|---|
| No Stroke | 0.95 | 1.00 | 0.97 | 947.00 |
| Stroke | 0.00 | 0.00 | 0.00 | 50.00 |
| accuracy | 0.95 | 0.95 | 0.95 | 0.95 |
| macro avg | 0.47 | 0.50 | 0.49 | 997.00 |
| weighted avg | 0.90 | 0.95 | 0.93 | 997.00 |

**Figure 9** shows the posterior forest plot for the nine β coefficients with 95% Highest Density Intervals. Age has by far the largest positive coefficient, followed by hypertension. Features whose HDI does not cross zero provide strong Bayesian evidence of a credible effect on stroke risk.

#### Bayesian Prior Sensitivity

##### Table 11: Bayesian Prior Sensitivity Analysis

| Feature | β Normal(0,2) | β Normal(0,1) | Δβ |
|---|---|---|---|
| age | 1.5669 | 1.5259 | -0.0410 |
| avg_glucose_level | 0.2181 | 0.2162 | -0.0020 |
| bmi | 0.0697 | 0.0676 | -0.0021 |
| hypertension | 0.5468 | 0.5218 | -0.0250 |
| heart_disease | 0.1955 | 0.1939 | -0.0016 |
| smoking_status_never smoked | -0.2888 | -0.3016 | -0.0128 |
| ever_married_Yes | -0.2202 | -0.3156 | -0.0954 |
| work_type_Self-employed | -0.0658 | -0.1366 | -0.0708 |
| work_type_Private | 0.2067 | 0.1144 | -0.0924 |

**AUC — Normal(0,2): 0.8479 | Normal(0,1): 0.8495**

All features have small Δβ values, meaning the conclusions are robust to the prior choice, and the data are sufficiently informative to dominate the prior.

#### Bayesian Posterior Odds Ratios

##### Table 12: Bayesian Posterior Odds Ratios and 95% Credible Intervals

| Feature | Post. mean β | 95% CrI lower β | 95% CrI upper β | Post. OR | OR CrI lower | OR CrI upper |
|---|---|---|---|---|---|---|
| age | 1.5669 | 1.3085 | 1.8394 | 4.7920 | 3.7008 | 6.2929 |
| avg_glucose_level | 0.2181 | 0.0981 | 0.3358 | 1.2438 | 1.1031 | 1.3990 |
| bmi | 0.0697 | -0.1147 | 0.2526 | 1.0722 | 0.8916 | 1.2874 |
| hypertension | 0.5468 | 0.1811 | 0.8714 | 1.7277 | 1.1985 | 2.3903 |
| heart_disease | 0.1955 | -0.2235 | 0.6212 | 1.2159 | 0.7997 | 1.8612 |
| smoking_status_never smoked | -0.2888 | -0.6099 | 0.0138 | 0.7492 | 0.5434 | 1.0139 |
| ever_married_Yes | -0.2202 | -0.6629 | 0.3300 | 0.8024 | 0.5153 | 1.3910 |
| work_type_Self-employed | -0.0658 | -0.5788 | 0.4409 | 0.9363 | 0.5606 | 1.5542 |
| work_type_Private | 0.2067 | -0.2187 | 0.6639 | 1.2296 | 0.8036 | 1.9423 |

`age` (OR = 4.79, CrI: 3.70–6.29) and `avg_glucose_level` (OR = 1.24, CrI: 1.10–1.40) show the clearest positive effects, with CrIs entirely above 1. `hypertension` (OR = 1.73, CrI: 1.20–2.39) similarly excludes 1, supporting a credible increase in stroke odds. These three align with the frequentist results.

The remaining predictors — `bmi`, `heart_disease`, `ever_married_Yes`, `work_type_Self-employed`, and `work_type_Private` — all have CrIs that cross 1, indicating insufficient evidence of a directional effect in this model. `smoking_status_never smoked` is borderline protective (OR = 0.75, CrI: 0.54–1.01), with the upper bound barely touching 1.

#### Posterior Predictive Checks

- **Observed stroke prevalence:** 0.0497
- **Mean replicated prevalence:** 0.0497
- **90% predictive interval for prevalence:** [0.0427, 0.0570]

**Figure 10** shows the distribution of stroke prevalence across 4,000 posterior predictive replications (each replication simulates a new dataset of the same size from the fitted model). The red dashed line marks the observed stroke prevalence in the training set.

#### WAIC and LOO (Bayesian Model Comparison)

##### Table 13: Bayesian Information Criteria (WAIC and LOO)

| Criterion | Estimate | p (eff. params) | SE |
|---|---|---|---|
| WAIC | -636.82 | 9.81 | 33.77 |
| LOO | -636.85 | 9.83 | 33.77 |

---

## Model Comparison

##### Table 14: Model Comparison — AUC-ROC

| Model | AUC-ROC | Notes |
|---|---|---|
| LPM (OLS) | 0.8396 | Linear baseline; predictions clipped to [0,1] |
| Frequentist Logistic | 0.8475 | Log-odds interpretation; MLE with p-values |
| Bayesian Logit | 0.8479 | Posterior mean; same 9 Lasso-selected features |

**Figure 11** overlays the ROC curves for all three models. The LPM sits slightly below the other two with an AUC of 0.840, while the Frequentist Logistic and Bayesian Logit curves are nearly indistinguishable at AUC = 0.848 each. The gap between LPM and the logistic models is small but consistent across most of the FPR range, suggesting that the logit link provides a modest but real improvement in ranking stroke risk over the linear baseline. The near-identical performance of the frequentist and Bayesian logistic models is expected since they share the same 9 features and functional form — the Bayesian posterior means are close to the MLE point estimates, so the predicted probabilities (and therefore rankings) barely differ. All three curves pull well above the diagonal random classifier line, confirming that the predictors in this dataset carry genuine discriminative signal for stroke, even though the absolute AUC (~0.84) leaves room for improvement.

### Classification Metrics and Precision-Recall Analysis

**Figure 12 (Confusion Matrices):** At the default 0.5 threshold, every single model predicts "No Stroke" for all 997 test observations — 947 correct (true negatives) and 50 missed strokes (false negatives), with zero true positives. This is not a bug; it reflects the base rate. When only ~5% of patients have strokes, the predicted probabilities almost never cross 0.5, so the classifier never triggers. The 95% accuracy is meaningless here — a coin that always lands tails would do the same. For any practical screening use, the threshold would need to be lowered substantially (to something like 0.15–0.20) to trade some false positives for actual stroke detection.

**Figure 13 (Precision-Recall Curves):** The PR curves give a more honest picture of minority-class performance than ROC. The horizontal dashed line at ~0.05 represents what a random classifier would achieve (equal to the stroke prevalence). All three models produce Average Precision around 0.18 (LPM AP = 0.183, Frequentist Logistic AP = 0.182, Bayesian Logit AP = 0.183), which is roughly 3.6 times better than random — meaningful but far from strong. The curves drop off steeply after about 40–50% recall, meaning that to catch more than half of stroke cases the model would need to accept very low precision (many false alarms per true detection). This tradeoff is typical of imbalanced medical datasets and underscores why AUC-ROC alone can be misleadingly optimistic.

---

## Part 3: 5-Fold Cross-Validation

Stratified 5-fold CV is used to obtain out-of-fold predicted probabilities for LPM and Frequentist Logistic Regression. The mean AUC and standard deviation across folds provide a more robust estimate of generalization performance than a single train/test split. Bayesian Logistic Regression is evaluated on the held-out test set only due to MCMC computational cost.

#### Table 15: 5-Fold Cross-Validation Results

| Model | Mean AUC | Std AUC |
|---|---|---|
| LPM | 0.8319 | 0.0173 |
| Frequentist Logistic | 0.8414 | 0.0206 |
| Bayesian Logistic | — | evaluated on held-out test set only (MCMC cost). Test AUC: 0.8479 |

---

## Part 4: Logistic Diagnostics — Linearity in the Logit

#### Table 16: Linearity Diagnostics — Linear vs. Nonlinear Logit

| Model | AIC | BIC | Test AUC |
|---|---|---|---|
| Linear logit (baseline) | 1273.78 | -31690.85 | 0.8475 |
| Logit + age² + glucose² | 1277.25 | -31674.80 | 0.8477 |

#### Table 17: Quadratic Term Coefficients

| Term | Coef. | Std.Err. | z | P>\|z\| |
|---|---|---|---|---|
| age_sq | -0.1166 | 0.1657 | -0.7034 | 0.4818 |
| avg_glucose_sq | -0.0043 | 0.0625 | -0.0687 | 0.9453 |

Adding the quadratic terms worsens both AIC (1273.78 → 1277.25) and BIC (-31690.85 → -31674.80), while Test AUC is essentially unchanged (0.8475 → 0.8477). Neither quadratic coefficient is statistically meaningful — age² (p = 0.48) and avg_glucose_level² (p = 0.95) are both far from significance, and both are negative, offering no evidence of super-linear acceleration in stroke risk.

The linear logit approximation is adequate for these predictors. The simpler 9-feature baseline is preferred on all three criteria: lower AIC, lower BIC, and equivalent discriminative performance.

---

## Part 5: Model Selection

### Lasso Logistic Regression (L1 Regularization)

- **CV-selected C:** 0.204336
- **Lasso Logistic — Test AUC:** 0.8480
- **Features retained (non-zero):** 9
- **Features zeroed out:** 5

#### Table 18: Lasso Logistic Regression Coefficients

| Feature | Coefficient |
|---|---|
| age | 1.425690 |
| hypertension | 0.394127 |
| avg_glucose_level | 0.220005 |
| smoking_status_never smoked | -0.185271 |
| ever_married_Yes | -0.083070 |
| work_type_Self-employed | -0.056227 |
| heart_disease | 0.027081 |
| bmi | 0.025860 |
| work_type_Private | 0.013356 |
| gender_Male | 0.000000 |
| work_type_children | 0.000000 |
| Residence_type_Urban | 0.000000 |
| smoking_status_formerly smoked | 0.000000 |
| smoking_status_smokes | 0.000000 |

### CV Comparison of Candidate Models

#### Table 19: Cross-Validation Comparison of Candidate Models

| Model | Mean AUC | Std AUC |
|---|---|---|
| Linear Logit | 0.8414 | 0.0206 |
| Nonlinear Logit | 0.8402 | 0.0194 |
| Lasso Logit | 0.8388 | 0.0215 |

All three models perform nearly identically — mean AUC differences are within one standard deviation, so no model is meaningfully better than another.

- **Linear Logit** (AUC = 0.841 ± 0.021) achieves the highest mean AUC with moderate variance, confirming that the 9 Lasso-selected main effects already capture most of the signal.
- **Nonlinear Logit** (AUC = 0.840 ± 0.019) adds age² and glucose² but gains nothing — consistent with the earlier finding that both quadratic coefficients are non-significant.
- **Lasso Logit** (AUC = 0.839 ± 0.022) has the most features and the highest variance, suggesting mild overfitting despite L1 regularization.

The Linear Logit is the preferred final model: it has the highest CV AUC, the fewest parameters among competitive models, and yields directly interpretable coefficients for inference.

### Post-Selection VIF (Lasso-Selected Features)

#### Table 20: Post-Selection Variance Inflation Factors (Lasso-Selected Features)

| Feature | VIF |
|---|---|
| ever_married_Yes | 3.36 |
| work_type_Private | 2.57 |
| work_type_Self-employed | 1.59 |
| age | 1.56 |
| smoking_status_never smoked | 1.56 |
| bmi | 1.20 |
| hypertension | 1.20 |
| heart_disease | 1.13 |
| avg_glucose_level | 1.10 |

All VIFs drop after Lasso selection, and the highest post-selection VIF (`ever_married_Yes`, 4.44 → 3.36) remains comfortably below 5. The five dropped features — `Residence_type_Urban`, `gender_Male`, `smoking_status_formerly_smoked`, `smoking_status_smokes`, and `work_type_children` — were the ones contributing most to collinearity. The retained 9-feature set shows no multicollinearity concerns, and the reduction in VIFs confirms that Lasso selection improved both parsimony and coefficient stability simultaneously.

---

## Part 6: Frequentist vs Bayesian Comparison

The main takeaway from comparing the two logistic models is that they tell the same story. Age comes out as the dominant predictor in both — the frequentist model gives it the largest log-odds coefficient by a wide margin (1.57, p < 0.001), and the Bayesian posterior for β(age) lands in essentially the same place with a 95% credible interval that stays far from zero. Hypertension is the second strongest effect in both frameworks: significant at p = 0.002 in the frequentist model, and with an HDI that clears zero in the Bayesian model. Average glucose level rounds out the top three, positive and credibly non-zero in both cases.

Every single coefficient points in the same direction across the two models. The risk-increasing features (`age`, `hypertension`, `avg_glucose_level`, `heart_disease`) are positive in both; the features associated with lower stroke odds (`smoking_status_never smoked`, `ever_married_Yes`) are negative in both. That kind of sign agreement across two different estimation frameworks is reassuring — it suggests the associations we're picking up are real patterns in the data rather than artifacts of how we happened to fit the model.

Where the two approaches do differ is in how they handle uncertainty. The Bayesian credible intervals tend to be a bit wider than the frequentist confidence intervals, especially for the weaker predictors like `bmi` and `heart_disease`. This makes sense: the Normal(0, 2) prior pulls coefficients slightly toward zero, which widens the interval for effects that the data can't pin down precisely. For a strong predictor like age, the prior barely matters — the data overwhelm it, and the frequentist CI and Bayesian CrI end up nearly identical. This is exactly what you'd want to see with weakly informative priors.

Both models agree that stroke risk in this dataset is primarily about age, with hypertension and glucose as secondary contributors. No lifestyle variable is strongly protective after adjusting for age. The Bayesian model doesn't change the clinical conclusions — it just adds posterior distributions on top of them, which is useful for communicating uncertainty but doesn't shift the story.

---

## Part 7: Final Recommended Model

### Recommendation

The frequentist logistic regression with the 9 Lasso-selected features is the recommended final model for stroke prediction on the Brain Stroke Dataset.

This choice follows directly from the results in Parts 2–5. Among the three candidate models compared via 5-fold stratified CV, the linear logit achieved the highest mean AUC (0.841 ± 0.021), narrowly ahead of the nonlinear logit (0.840 ± 0.019) and Lasso logit (0.839 ± 0.022). Since the differences fall well within one standard deviation, none of the three is meaningfully superior in discriminative ability — but the linear logit is the most parsimonious and the most interpretable, making it the natural default.

From a theoretical standpoint, logistic regression is the appropriate generalized linear model for a binary response. It constrains predicted probabilities to (0, 1) through the logit link, unlike the LPM which can produce out-of-range predictions. Coefficients on the log-odds scale exponentiate directly to odds ratios, giving each predictor a straightforward clinical interpretation. Maximum likelihood estimation through `statsmodels` provides standard errors, z-statistics, p-values, and confidence intervals for each coefficient — all of which are reported in Part 2.

The Bayesian logistic regression (AUC = 0.848 on the held-out test set) performs comparably and adds genuine value through posterior uncertainty quantification. The prior sensitivity check in Part 2 confirmed that conclusions are robust to the choice between Normal(0, 2) and Normal(0, 1) priors, and the posterior predictive check showed the model reproduces the observed 5% stroke prevalence well. However, its computational overhead (MCMC sampling) and the additional complexity of reporting posterior intervals make the frequentist version more practical as a primary recommendation.

### Key Predictors

The three predictors with statistically significant effects in the frequentist model — and whose Bayesian credible intervals exclude zero — are **age** (OR ≈ 4.81, p < 0.001), **hypertension** (OR ≈ 1.73, p = 0.002), and **average glucose level** (OR ≈ 1.24, p < 0.001). Age dominates: each standard-deviation increase corresponds to roughly a fivefold increase in stroke odds. Heart disease shows a positive but non-significant effect (OR ≈ 1.24, p = 0.33), likely due to limited statistical power given only ~215 heart disease cases in the training data. The remaining features — BMI, marital status, work type, and smoking status — were retained by Lasso but do not reach significance in either framework.

### Limitations

The most consequential limitation is class imbalance. With a ~5% stroke rate, all three models predict "No Stroke" for every test observation at the default 0.5 threshold, yielding zero recall for the positive class. The confusion matrices in Part 2 make this clear. In a clinical screening context, a much lower threshold (perhaps 0.15–0.20) would be necessary to flag at-risk patients, at the cost of more false positives. Alternatively, class-weighted training or oversampling techniques like SMOTE could shift the predicted probability distribution upward for stroke cases.

The training set contains only about 198 stroke cases, which limits power for detecting effects of weaker predictors and may produce noisy coefficient estimates for features like smoking status or work type. A larger or enriched dataset would help here.

Finally, the analysis is limited to the variables available in the Brain Stroke Dataset. Important clinical predictors like atrial fibrillation status, cholesterol levels, medication use, and family history are absent. Any real deployment of a stroke risk model would need a richer feature set, and the AUC values reported here (around 0.84) should be interpreted in that context — the models are doing reasonably well given what they have to work with, but they are not capturing the full clinical picture.

---

## References

- World Health Organization. (2025). Stroke.
- Centers for Disease Control and Prevention. (2024). Cardiovascular disease indicator definition.
- Boehme, A. K., Esenwa, C., & Elkind, M. S. V. (2017). Stroke risk factors, genetics, and prevention.
- Feigin, V. L., et al. (2024). Global, regional, and national burden of stroke and its risk factors, 1990–2021.
