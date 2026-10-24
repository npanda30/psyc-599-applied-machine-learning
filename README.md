# PSYC559 Project 1, Part 2: Predicting Depression Severity from NHIS 2025

Predicting whether a U.S. adult screens at **moderate-to-severe depressive symptom severity** (PHQ-8) from routine demographic, socioeconomic, health-status, and health-behavior variables in the 2025 National Health Interview Survey. The target is dichotomous, so the analysis uses regularized logistic regression.

**Author:** Neha Panda
**Course:** PSYC 559 — Applied Machine Learning for Psychology

## Contents

- `Project1_Part2_PandaNeha.Rmd` — analysis code: recoding, missing-data handling, modeling pipeline, and model comparison. Runs start to finish from the raw data.
- `Project1_Part2_PandaNeha.pdf` — knitted output of the `.Rmd` (code, results, and figures).
- `PSYC559_Project_1_Report.pdf` — written report (abstract, introduction, method, results, discussion, references).

## Data

- **Source:** 2025 NHIS Sample Adult public-use file (National Center for Health Statistics).
- **Access:** the `.Rmd` downloads and unzips the file automatically from the NCHS server on first run (`adult25csv.zip` → `adult25.csv`); later runs reuse the local copy. An internet connection is required for that first download.
- **Analysis sample:** 24,215 respondents and 577 variables in the raw file; 11 variables selected (10 predictors + target); 23,619 rows after dropping cases with a missing target.

## How to reproduce

1. Open `Project1_Part2_PandaNeha.Rmd` in RStudio.
2. Install the required packages (below) if needed.
3. Knit. The first knit downloads the data (~22 MB); subsequent knits reuse it.

### Required R packages

`naniar`, `dplyr`, `ggplot2`, `rsample`, `recipes`, `caret`, `glmnet`

```r
install.packages(c("naniar", "dplyr", "ggplot2", "rsample", "recipes", "caret", "glmnet"))
```

## Method summary

- Categorical variables recoded to labeled factors; ordinal variables (education, income-to-poverty ratio, self-rated health) kept numeric; NHIS non-response codes (7/8/9, 97/98/99) converted to `NA`.
- Target: PHQ-8 severity category collapsed to moderate-to-severe (3–4) vs. none-to-mild (1–2). The original category and the eight PHQ-8 item variables were excluded to avoid outcome leakage.
- 50/50 train/test split, stratified on the target. Preprocessing (median/mode imputation, dummy-coding, zero-variance removal, centering/scaling) runs inside the recipe so it is learned within each cross-validation fold.
- Five models compared with 10-fold cross-validation: intercept-only baseline, unpenalized logistic, and lasso, ridge, and elastic-net logistic. Model selection used **log loss** (appropriate for an imbalanced binary outcome).

## Key result

The lasso was selected (lowest CV log loss, ~0.203), but regularization gave a negligible improvement over unpenalized logistic regression. On the test set it reached 93% accuracy, which did not exceed the no-information rate (p = .12), with high specificity (0.994) and very low sensitivity (0.115). The routine predictors carry weak individual-level signal for depression; the model is not usable as a screener as-is. Strongest predictors: poorer self-rated health, younger age, and disability.
