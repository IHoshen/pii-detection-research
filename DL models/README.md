# Stage 2b — Deep Learning Classification

This directory contains the deep learning predictive branch of the pipeline.
It runs in parallel to the tree-based branch (`DT models/`), consuming the
same labelled input produced in Stage 1 (`Bert Models/`).

The two branches are independent: they address the same prediction task
through different modelling families, allowing their performance to be
compared directly.

---

## Input

The labelled dataset derived from the Stage 1 detection output:

| Field | Description |
|---|---|
| `PII` | Binary target — whether the post contains PII (1 / 0) |
| `mean_exposure` | Aggregate engagement measure — likes, shares, reactions, comments |
| *text features* | Derived from post content |

Within `Facebook/`, `class_df.csv` holds this prepared classification table.
It is not tracked in this repository.

---

## Contents

```
DL models/
├── Facebook/
│   ├── class_df.csv                  # not tracked — prepared input table
│   ├── BoW & BiLSTM/
│   │   ├── DL Facebook - BoW.ipynb
│   │   ├── DL Facebook - BiLSTM + new data.ipynb
│   │   └── DL Facebook - BiLSTM_FINAL.ipynb
│   └── Fine-Tuning/
│       └── DL_Facebook_Fine_Tuning_V1.ipynb
│
├── LinkedIn/                         # planned
└── X (Twitter)/                      # planned
```

---

## Models

**Bag-of-Words** (`DL Facebook - BoW.ipynb`)
A feed-forward baseline over sparse Bag-of-Words features, establishing a
reference point for the sequential architectures that follow.

**BiLSTM** (`DL Facebook - BiLSTM + new data.ipynb`, `DL Facebook - BiLSTM_FINAL.ipynb`)
A bidirectional LSTM over token sequences, capturing contextual dependencies
that the Bag-of-Words representation discards. The first notebook documents an
iteration on an expanded dataset; `BiLSTM_FINAL` is the version of record.

**Fine-Tuning** (`DL_Facebook_Fine_Tuning_V1.ipynb`)
Experiments in adapting a pre-trained transformer to the classification task
directly, rather than relying on features derived from the Stage 1 output.

---

## Status

| Platform | Status |
|---|---|
| Facebook | Implemented — BoW, BiLSTM, fine-tuning |
| LinkedIn | Planned |
| X (Twitter) | Planned |

The `LinkedIn/` and `X (Twitter)/` directories are intentionally present but
empty. They reserve the structure for experiments scheduled as the research
continues; each holds a `.gitkeep` placeholder so the directory persists in
version control.

Note that both platforms are already complete in the tree-based branch
(`DT models/`) — the gap is specific to the deep learning experiments.
