# Stage 1 — PII Extraction with BERT

This directory contains the first stage of the pipeline: collecting public
posts from each social network and extracting Personally Identifiable
Information (PII) from their text.

The workflow is **identical across all three platforms**. Each platform
directory — `Facebook/`, `LinkedIn/`, `X (Twitter)/` — holds the same five
artefacts, differing only in source data.

---

## Workflow

```
1. Scraping
   Public posts collected via commercial automated scraping tools.
   Each session logged in:  [platform] Scraping documentation.xlsx
                │
                ▼
2. Aggregation
   Scraping tools export one CSV per source page. These are merged
   into a single per-platform dataset by:
                        [platform] - dataset_builder.ipynb
                │
                ▼
3. Consolidated dataset  →  [platform] dataset.csv
                │
                ▼
4. Pre-processing + PII detection
   Text cleaning, then token classification using the pre-trained
   model ab-ai/pii_model (Hugging Face), refined with regular
   expressions and gazetteer lookups:
                        [platform]_Bert_Model_V1.ipynb
                │
                ▼
5. Detection output
   [platform] pii_detection_results_with_regex_and_gazetteer.csv
                │
                ▼
6. Statistical analysis of the detection results, producing the
   labelled input consumed by Stage 2 (see DT models/ and DL models/).
```

---

## Files in each platform directory

| File | Type | Role |
|---|---|---|
| `[platform] Scraping documentation.xlsx` | Tracked | Log of the scraping sessions — source pages, dates, volume collected |
| `[platform] - dataset_builder.ipynb` | Tracked | Merges the per-page CSV exports into one consolidated dataset |
| `[platform] dataset.csv` | **Not tracked** | Consolidated raw dataset — output of the builder notebook |
| `[platform]_Bert_Model_V1.ipynb` | Tracked | Pre-processing, BERT inference, regex + gazetteer refinement, statistical analysis |
| `[platform] pii_detection_results_with_regex_and_gazetteer.csv` | **Not tracked** | Final detection output — input to Stage 2 |

CSV files are excluded from this repository via `.gitignore`.
See the *Data Availability* section of the root `README.md`.

---

## Detection method

**Base model** — [`ab-ai/pii_model`](https://huggingface.co/ab-ai/pii_model),
a pre-trained token-classification model for PII recognition. All processed
content is in English; non-English text was removed during pre-processing.

**Refinement layer** — the neural model is complemented by two rule-based
components:

- **Regular expressions** for structurally regular identifiers such as email
  addresses, phone numbers, and URLs, where pattern matching is more reliable
  than learned representations.
- **Gazetteer lookups** against curated entity lists, improving recall for
  named entities the model handles inconsistently.

The combination is reflected in the output filename
(`..._with_regex_and_gazetteer.csv`) and was independently assessed through
manual validation — see `Validation/`.

---

## Notes

- The three platforms were processed independently; no cross-platform
  entity linking was performed.
- Notebook outputs containing raw post text have been cleared prior to
  publication.
