# Stage 2a — Tree-Based Classification

This directory contains the tree-based predictive branch of the pipeline.
It consumes the PII detection output produced in Stage 1 (`Bert Models/`)
and predicts whether a post constitutes a privacy risk.

---

## Input

The labelled dataset derived from
`[platform] pii_detection_results_with_regex_and_gazetteer.csv`:

| Field | Description |
|---|---|
| `PII` | Binary target — whether the post contains PII (1 / 0) |
| `mean_exposure` | Aggregate engagement measure — likes, shares, reactions, comments |
| *text features* | Derived from post content (see below) |

Input CSV files are not tracked in this repository.

---

## Contents

| Path | Role |
|---|---|
| `Facebook/Facebook Classification Model.ipynb` | Tree-based classification — Facebook |
| `LinkedIn/LinkedIn Classification Model.ipynb` | Tree-based classification — LinkedIn |
| `X (Twitter)/X Classification Model.ipynb` | Tree-based classification — X |
| `Classification Model Gemini.ipynb` | External LLM baseline for comparison |

---

## Methodology

**Data splitting**
Stratified train/test splitting preserving class proportions, repeated across
five independent splits to assess stability. Stratified and random splitting
were compared explicitly to justify the stratified approach.

**Feature engineering — row level**
Basic numerical features per text row: word count, character count, digit
count, number count. Log-transformed variants were added to handle
right-skewed, long-tailed distributions.

**Feature engineering — text level**
Bag-of-Words and TF-IDF representations. All numerical features were scaled,
fitting only on the training set to prevent leakage. Features were combined
into a single sparse matrix.

**Distribution treatment**
Square-root and low-power (0.3) transformations were tested but did not
meaningfully improve normality. A Gaussian RBF approach combined with KMeans
was also attempted without success. Quantile binning into five equal groups
produced a uniform distribution and was retained as the primary
transformation; unsuccessful variants were dropped.

**Models and tuning**
Decision Tree, Random Forest, and XGBoost, each tuned via
`RandomizedSearchCV`. Evaluation across all five splits used **Macro-F1** as
the primary metric, appropriate given class imbalance.

**Results**
XGBoost achieved the strongest performance and was selected as the final
model, with explicit handling of class imbalance. It was further validated
through cross-validation across the five splits, with per-class precision,
recall, and F1 reported.

A full narrative account of these experiments is available in
`Pred_Documentation.docx` in the repository root.

---

## Gemini baseline

`Classification Model Gemini.ipynb` applies a large language model to the same
classification task, serving as an external reference point against which the
tree-based results can be compared. It is a baseline, not part of the primary
modelling pipeline.
