# PII Leakage Detection in Online Social Networks

A research project on the automated detection of Personally Identifiable
Information (PII) exposure in public posts across Online Social Networks (OSNs).

---

## 1. Research Objective

Users of online social networks routinely disclose personal information in
public posts — often without recognising the privacy risk. This project
develops an AI-based mechanism that identifies such disclosures and estimates
their potential impact.

The work addresses two questions:

1. **Detection** — Can a transformer-based model reliably identify PII
   entities within unstructured, user-generated social media text?
2. **Prediction** — Given the detected entities and a post's measured
   exposure, can classification models predict whether a post constitutes a
   privacy risk?

The project covers three platforms: **Facebook**, **LinkedIn**, and
**X (Twitter)**. All analysed content is in English.

---

## 2. Pipeline Overview

The research follows a two-stage architecture. Stage 1 extracts PII;
Stage 2 branches into two independent predictive approaches.

```
                    ┌─────────────────────────────┐
                    │  STAGE 0 — Data Collection  │
                    └─────────────────────────────┘
                                  │
        Automated scraping (commercial tooling), per platform
                                  │
        Scraping log  →  [platform] Scraping documentation.xlsx
                                  │
        Per-page CSV exports  →  aggregated into one dataset
                    [platform] - dataset_builder.ipynb
                                  │
                                  ▼
                       [platform] dataset.csv
                                  │
                    ┌─────────────────────────────┐
                    │  STAGE 1 — PII Extraction   │
                    └─────────────────────────────┘
                                  │
        Pre-processing  →  BERT inference  →  regex + gazetteer refinement
                    [platform]_Bert_Model_V1.ipynb
                                  │
                                  ▼
      [platform] pii_detection_results_with_regex_and_gazetteer.csv
                                  │
        Statistical analysis of detection results
                                  │
        Binary labelling (PII present: 1 / 0)  +  mean_exposure
                                  │
                    ┌─────────────────────────────┐
                    │  STAGE 2 — Prediction       │
                    └──────────────┬──────────────┘
                                   │
                   ┌───────────────┴───────────────┐
                   ▼                               ▼
        Tree-based classifiers            Deep learning models
        (DT / RF / XGBoost)               (BoW / BiLSTM / Fine-tuning)
              DT models/                        DL models/
```

---

## 3. Repository Structure

```
pii-detection-research/
│
├── Data Samples/             # raw scraped data samples (one CSV per platform)
│   ├── facebook dataset - sample.csv
│   ├── linkedin dataset - sample.csv
│   └── x dataset - sample.csv
│
├── Bert Models/              # Stage 1 — PII extraction
│   ├── Facebook/
│   ├── LinkedIn/
│   └── X (Twitter)/
│
├── DT models/                # Stage 2a — tree-based classification
│   ├── Facebook/
│   ├── LinkedIn/
│   ├── X (Twitter)/
│   └── Classification Model Gemini.ipynb
│
├── DL models/                # Stage 2b — deep learning
│   ├── Facebook/
│   ├── LinkedIn/             # planned
│   └── X (Twitter)/          # planned
│
├── Validation/               # manual validation protocol
│
├── Pred_Documentation.docx
├── ProgrammingTasksForAIPrivacyResearch.docx
└── .gitignore
```

Each stage directory contains its own `README.md` describing the files
within it.

`Data Samples/` holds raw data: samples of the posts scraped from each
social network (Facebook, LinkedIn, X), before any processing or PII
extraction.

---

## 4. Methodology

### Stage 0 — Data Collection

Public posts were collected per platform using commercial automated
scraping services. Each scraping session is logged in
`[platform] Scraping documentation.xlsx`, which records the source pages,
collection dates, and volume retrieved.

Because the scraping tools export one CSV per source page, a dedicated
aggregation notebook (`[platform] - dataset_builder.ipynb`) consolidates
these into a single per-platform dataset.

### Stage 1 — PII Extraction (BERT)

PII entities are extracted using the pre-trained token-classification model
[`ab-ai/pii_model`](https://huggingface.co/ab-ai/pii_model) from Hugging Face.

Model output is refined through a complementary layer of **regular
expressions** and **gazetteer lookups**, which capture structured identifiers
(emails, phone numbers, URLs) and known-entity lists that the neural model
alone handles inconsistently. The combined results are then analysed
statistically to characterise PII distribution across platforms.

### Stage 2 — Predictive Classification

The refined detection output is converted into a supervised learning dataset:

| Field | Description |
|---|---|
| `PII` | Binary target — whether the post contains PII (1 / 0) |
| `mean_exposure` | Aggregate engagement measure (likes, shares, reactions, comments) |
| *text features* | Derived from post content |

**Stage 2a — Tree-based models** (`DT models/`)
Decision Tree, Random Forest, and XGBoost, with stratified train/test
splitting across five independent splits, Bag-of-Words and TF-IDF text
representations, engineered row-level numerical features (word, character,
digit and number counts, with log and quantile-binned transformations), and
`RandomizedSearchCV` hyperparameter tuning. Macro-F1 was used as the primary
metric given class imbalance. XGBoost achieved the strongest performance.
A Gemini-based classification notebook serves as an external baseline.

**Stage 2b — Deep learning models** (`DL models/`)
Bag-of-Words and BiLSTM architectures, together with fine-tuning experiments.
Currently implemented for Facebook; LinkedIn and X are planned.

### Validation

A manual validation protocol was applied to a stratified sample of Facebook
posts to verify detection quality independently of automated metrics. See
`Validation/`.

---

## 5. Data Availability

**No datasets are included in this repository.**

The full corpus is approximately 3.2 GB and exceeds GitHub's practical
storage limits. In addition, the raw material consists of publicly posted
social media content that may contain personal information; redistributing
it would be inconsistent with the privacy objectives of this research.

All intermediate and final CSV outputs — per-platform datasets, PII detection
results, and the labelled classification tables — are excluded via
`.gitignore` and maintained in controlled storage by the author.

Data structure, provenance, and volume are documented in the
`Scraping documentation.xlsx` files and in the per-stage `README.md`
documents, which are included in this repository. Access to the underlying
data may be requested for academic review purposes.

---

## 6. Ethics and Privacy

This research analyses publicly accessible social media content for the
purpose of improving user privacy protection.

- Data collection was conducted under institutional approval, using
  commercial scraping services that enforce platform-level access
  restrictions.
- Only content that was publicly published and not protected by any privacy
  setting was collected. No private, restricted, or authentication-gated
  material was accessed.
- Full datasets are not distributed through this repository. Analytical
  notebooks and validation records retain illustrative samples of the
  collected content, all of which was publicly visible at the time of
  collection.
- Detected PII is used exclusively for aggregate statistical analysis and
  model training. No individual is profiled, tracked, or identified in any
  research output.
- This repository is currently private and access is restricted to the
  author and authorised reviewers.

---

## 7. Requirements

| Component | Purpose |
|---|---|
| Python 3.9+ | Runtime |
| `transformers`, `torch` | BERT inference (Stage 1) |
| `scikit-learn`, `xgboost` | Tree-based classification (Stage 2a) |
| `tensorflow` / `keras` | BiLSTM models (Stage 2b) |
| `pandas`, `numpy`, `scipy` | Data handling and statistics |
| `matplotlib`, `seaborn` | Visualisation |

Notebooks were developed in Google Colab and Jupyter.

---

## 8. Project Status

| Component | Facebook | LinkedIn | X (Twitter) |
|---|:---:|:---:|:---:|
| Data collection | ✅ | ✅ | ✅ |
| BERT extraction | ✅ | ✅ | ✅ |
| Tree-based models | ✅ | ✅ | ✅ |
| Deep learning models | ✅ | ⏳ planned | ⏳ planned |
| Manual validation | ✅ | — | — |

Active research project — under continued development.
