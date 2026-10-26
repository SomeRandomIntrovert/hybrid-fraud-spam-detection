# Hybrid Fraud & Spam Detection Framework

A rule-driven detection framework that flags suspicious review submissions before a human ever opens the queue. SQL enriches each submission with additional intelligence, and a formula-driven rule engine encodes the patterns fake-review submitters are known to follow. The output is a deduplicated, flag-labeled list of review IDs, each with the reason it was flagged.

Built for review content operations at a high-volume B2B software review platform. **The model deliberately contains no AI or LLM component**: the data it processes is confidential company information, so detection is transparent, auditable, and runs entirely on SQL and spreadsheet logic.

## The problem

Manual fraud and spam review was slow and inconsistent, creating risk of undetected operational losses from fraudulent/spam reviews. Manual detection accuracy sat at roughly 72-78%.

## How it works

```
Raw review data pasted into Input tab
        |
        v
SQL query: Additional intelligence lookup
   -> results pasted back into the workbook
        |
        v
Formula-driven rule engine
        |
        v
Final Output: deduplicated review IDs, with what is suspicious about each
```

Everything after the SQL step is formula-driven. Paste, run, read.

## Detection Signals

Intentionally not published. Detection depends on submitters not knowing what is tracked, so publishing the signals would let bad actors adapt. This repo documents the architecture, design decisions, and validation approach instead.

## Design decisions

- **No LLM, on purpose.** The input contains confidential company data that cannot be handed to external models. Rule-based logic also makes every flag explainable: each flagged review shows exactly which pattern triggered it.
- **Formula-driven, so analysts can run it.** No code deployment. A non-technical analyst pastes data, runs one query, and reads the result.
- **Tuned against false positives.** A change log records each rule adjustment made after a pattern over-flagged legitimate reviewers.

## Results

Detection accuracy improved from a **72-78% manual baseline to 92%**, measured on internal testing data. That data is confidential and is not included, so the figure cannot be independently reproduced from this repo. However, the validation approach can be found below.

## Validation Approach

Accuracy was tested two ways. The results and sample dataset are confidential and not published.

1. **Historical backtest.** The model was run on past reviews that included fraudulent reviews the team had correctly identified and fraudulent reviews the team had missed. It was checked on both: whether it caught what the team caught, and whether it caught what the team missed.

2. **Control batch test.** A fixed set of reviews, including reviews following known spam and fraud patterns, was given to processors for manual review and run through the model, so both could be compared on the same batch.

**Ground truth.** Test cases were clear-cut spam, with unambiguous signs of fraud on proper analysis, not borderline cases.

**False positives.** Some occurred but were low. Because the model produces flags for review, a human makes the final decision on every flagged record.

## What is not in this repo

- Raw review data and the input column schema
- Accuracy testing data
- Detection Signals and Rule Logic
- Any internal table names or company-specific identifiers

## Limitations

- **Rule-based, so spammers can adapt.** Watchlists and patterns need ongoing maintenance as behavior shifts.
- **Flags indicate suspicion, not a verdict.** Output is a pre-triaged queue for analyst review.
- **Platform dependency.** The workbook was built in Google Sheets and relies on dynamic-array functions.
- **Accuracy figures come from internal testing** that cannot be shared.

## Stack

SQL, Google Sheets (dynamic-array formulas)

## Author

Bhargava Bhavaraju. Trust & Safety and Fraud Risk Operations Analyst. [LinkedIn](https://linkedin.com/in/bhargava-bhavaraju) | [Portfolio](https://somerandomintrovert.github.io/bhargavabhavaraju)
