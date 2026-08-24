# Manual Validation

This directory documents a manual validation of the Stage 1 PII detection
output — the combined result of BERT inference, regular expressions, and
gazetteer lookups produced in `Bert Models/`.

The purpose is to assess detection quality by direct human review of the
model's predictions, independently of automated metrics computed on
model-generated labels.

---

## Scope

| Property | Value |
|---|---|
| Platform | Facebook only |
| Sample size | 50 posts |
| Subject of review | Stage 1 detection output (BERT + regex + gazetteer) |
| Method | Manual inspection of each post against its predicted PII entities |

The validation covers a single platform. It was not repeated for LinkedIn or
X (Twitter); results reported here should not be assumed to generalise to
those platforms.

---

## Contents

| File | Description |
|---|---|
| `50 Samples validation.xlsx` | Per-post review record for the 50-post sample |
| `Validation of 50 posts.txt` | Accompanying notes and observations |

---

## Interpretation

Automated evaluation of the pipeline relies on labels that are themselves
derived from the detection process. Manual review provides an external check
on that process: it establishes whether the entities the system reports as
PII genuinely are PII, and whether disclosures present in the text were
missed.

Findings from this review informed the refinement of the regex and gazetteer
layer described in `Bert Models/README.md`.

---

## Note on content

## Note on content

The validation records include the reviewed post text, which is necessary to
make each detection judgement auditable. All content was publicly published
and unrestricted at the point of collection, and is retained here solely as
supporting evidence for the review.
