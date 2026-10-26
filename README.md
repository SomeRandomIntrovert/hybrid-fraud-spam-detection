# Hybrid Fraud & Spam Detection Framework

A rule-driven detection framework that flags suspicious review submissions before a human ever opens the queue. SQL enriches each submission with IP intelligence (VPN, proxy, Tor), and a formula-driven rule engine encodes the patterns fake-review submitters are known to follow. The output is a deduplicated, flag-labeled list of review IDs, each with the reason it was flagged.

Built for review operations at a high-volume B2B software review platform. **The model deliberately contains no AI or LLM component**: the data it processes is confidential company information, so detection is transparent, auditable, and runs entirely on SQL and spreadsheet logic.

## The problem

Fake reviews on a review platform come from a few recurring sources:

- **Incentivized campaigns:** reviewers paid to submit reviews
- **Vendor-driven reviews:** a vendor generating positive reviews for its own product
- **Third-party incentives:** outside organizations rewarding reviewers for submitting

Spotting these manually meant analysts re-learning the same patterns on every batch, with inconsistent results. Manual detection accuracy sat at roughly 72-78%.

## How it works

```
Raw review data pasted into Input tab
        |
        v
SQL query: IP intelligence lookup (VPN / proxy / residential proxy / Tor)
   -> results pasted back into the workbook
        |
        v
Formula-driven rule engine (seven detectors)
        |
        v
Final Output: deduplicated review IDs, with what is suspicious about each
```

Everything after the SQL step is formula-driven. Paste, run, read.

## The seven detectors

| Detector | What it looks for |
|---|---|
| Location mismatch | Country the reviewer selected vs. the country implied by their email domain |
| Probable identity theft | Username patterns typical of throwaway accounts, combined with country or anonymized-IP signals |
| Odd domain | Non-standard email domain extensions on non-consumer addresses |
| Educational-domain spam | Educational email domains paired with mismatch signals |
| Repeated-source pattern | Submission-source signatures historically associated with spam |
| Sanctioned / high-risk country | Submissions from countries on a maintained risk list |
| Name-username mismatch | Tiered similarity scoring between reviewer name, username, company and job title |

The exact country, ISP, and domain lists, the scoring thresholds, and the input schema are not published here (see "What is not in this repo").

## Design decisions

- **No LLM, on purpose.** The input contains confidential company data that cannot be handed to external models. Rule-based logic also makes every flag explainable: each flagged review shows exactly which pattern triggered it.
- **Formula-driven, so analysts can run it.** No code deployment. A non-technical analyst pastes data, runs one query, and reads the result.
- **SQL for what formulas cannot do.** IP anonymity status (VPN, proxy, Tor) comes from a lookup that a spreadsheet cannot compute on its own.
- **Tuned against false positives.** A change log records each rule adjustment made after a pattern over-flagged legitimate reviewers.

## Results

Detection accuracy improved from a **72-78% manual baseline to 92%**, measured on internal testing data. That data is confidential and is not included, so the figure cannot be independently reproduced from this repo.

## What is in this repo

```
README.md
docs/
  architecture.md        Flow and design notes
  detectors.md           Detector descriptions (no lists or thresholds)
  change_log.md          False-positive tuning history, generalized
screenshots/             Sanitized views of the logic tabs
```

## What is not in this repo

- Raw review data and the input column schema
- Accuracy testing data
- Country, ISP, and domain watchlists, and exact scoring thresholds
- Any internal table names or company-specific identifiers

## Limitations

- **Rule-based, so spammers can adapt.** Watchlists and patterns need ongoing maintenance as behavior shifts.
- **Flags indicate suspicion, not a verdict.** Output is a pre-triaged queue for analyst review.
- **Platform dependency.** The workbook was built in Google Sheets and relies on dynamic-array functions.
- **Accuracy figures come from internal testing** that cannot be shared.

## Stack

SQL, Google Sheets (dynamic-array formulas), IP intelligence lookup.

## Author

Bhargava Bhavaraju. Trust & Safety and Fraud Risk Operations Analyst. [LinkedIn](https://linkedin.com/in/bhargava-bhavaraju) | [Portfolio](https://somerandomintrovert.github.io/bhargavabhavaraju)
