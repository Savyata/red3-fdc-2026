# Red3-FDC-2026: An Offset-Preserving, Deduplicated Dataset of FD&C Red No. 3 Declarations in U.S. Branded Foods, with Human-Adjudicated Validation Labels

**Audit pipeline, summary tables, and annotated validation sample from the USDA FoodData Central Branded Foods release of 2026-10-29.**

Version 1.0.0 · USDA release analysed: 2026-10-29 · DOI: [10.5281/zenodo.24488536](https://doi.org/10.5281/zenodo.24488536)

This repository holds one Jupyter notebook and the tables it produced. The notebook downloads a USDA FoodData Central Branded Foods release, classifies every ingredient statement by whether it declares FD&C Red No. 3 (Red 3, erythrosine), reduces the records to barcodes and declared formulations, and scores the classifier against 716 ingredient statements labelled independently by two people.

**What is measured.** Whether a submitted database ingredient statement *declares* FD&C Red No. 3. A declaration is a property of a database record. It does not establish chemical presence, concentration, retail availability, or regulatory non-compliance, and a statement without a declaration does not prove the dye is absent.

## Results for the 2026-10-29 release

All figures come from run `run_20261124T044933_996155Z_db1a115f` and are stored in `tables/audit_qc.json` and the CSV tables.

| Unit of analysis | Count | Declares Red No. 3 |
|---|---:|---:|
| Source records | 2,010,212 | 36,047 detected (1.79%); 1,035 held for review |
| Latest record per eligible U.S. barcode (GTIN) | 455,546 | 9,127 detected (2.00%); 275 held for review |
| Declared formulation variants | 1,022,374 | see `owner_summary.csv`, `category_summary.csv` |
| Product identities (package sizes merged) | 903,146 | see `freshness_by_cohort.csv` |

**Read the barcode figure together with record age.** Of the 9,127 latest barcodes with a detected declaration, 38 (0.42%) have a latest record published within 12 months of the analysis date, 47 (0.51%) within 24 months, and 3,512 (38.48%) within 48 months. The remaining 5,615 (61.52%) were last published more than 48 months earlier. The count describes what the database holds, not what is on sale.

Further detail:

- **Record status.** 1,967,756 records are `not_detected` and 5,374 are `unknown` (missing or placeholder ingredient statement). The 37,082 detected and review records contain 39,834 name mentions.
- **U.S. cohort.** 2,009,095 records name the United States as primary market. 1,963,525 have a syntactically valid barcode and 45,570 do not. Of the 455,546 eligible barcodes, 391,783 have more than one record and 27 have conflicting records on their latest date.
- **Latest barcode status.** 443,788 `not_detected`, 9,127 `detected`, 2,356 `unknown`, 275 `review`. With unknown and review excluded, 9,127 of 452,915 is 2.02%.
- **Names used.** 37,032 matched records (99.87%) use a numeric Red 3 name. 113 use E127, 92 use "erythrosine", and 1 uses a Colour Index number. No record uses INS 127, Food Red 14, Acid Red 51, or a chemical name.
- **Lake and base forms.** 32,620 records declare only the base dye, 4,092 only the lake, and 370 both.
- **Declaration histories.** Among 390,030 barcodes with interpretable records on at least two dates, 382,256 never declare the dye, 7,486 always do, 116 change from declared to not declared, and 172 change the other way. A change in a database record is not evidence of reformulation.
- **Categories with the most detected latest barcodes.** Candy (3,013 of 23,073), Cookies & Biscuits (1,566 of 13,493), Baking Decorations & Dessert Toppings (1,094 of 4,148), and Cakes, Cupcakes, Snack Cakes (911 of 9,425).
- **Position in the statement.** Among 9,038 matches in 8,299 distinct statements, 53.05% sit at the top level of the ingredient list, 25.46% inside a colour group, and 21.49% inside a bracketed sub-component. A further 681 matches were excluded because the statement has unbalanced brackets.

## Repository contents

```
Red3_Complete_Pipeline.ipynb        the whole pipeline, 28 sections, executed
requirements-installed.txt          package versions of the run (pip freeze)
data/annotations/red3_consolidated/ the two annotators' labels and the adjudicated label
outputs/consolidated_runs/<run_id>/
    tables/                         CSV tables and audit_qc.json
    processed/gold_standard_benchmark.parquet
    figures/record_freshness_and_category.png
LICENSE, CITATION.cff, README.md
```

The USDA archive, the extracted tables, and the per-record Parquet files are not stored here. The notebook downloads and rebuilds them.

## Data source and provenance

| Item | Value |
|---|---|
| Source | USDA FoodData Central, Branded Foods CSV release of 2026-10-29 |
| URL | `https://fdc.nal.usda.gov/fdc-datasets/FoodData_Central_branded_food_csv_2026-10-29.zip` |
| Archive size | 451,954,262 bytes |
| `food.csv` SHA-256 | `505288ee4ae062805181bd8b5c034efa9c682ceb58a590ef2476fa46cc9622d3` (377,990,607 bytes) |
| `branded_food.csv` SHA-256 | `a68b6ed1ce76182fa4eb7d1730a4fb2b49d2c683ca9d1dfcfc51c1a03c3b7013` (960,715,079 bytes) |
| Rule-set fingerprint | `267c2b303d19b3aa348bb4323978fda3895d6dea79de71f43601a972dddc7122` |
| Freshness reference date | 2026-11-21 (`ANALYSIS_DATE`, fixed in Section 1) |

**Verify the tables, not the archive.** USDA serves this release in more than one ZIP packaging. The member files are byte-identical, but the ZIP timestamps differ, so the archive hash depends on which copy the server sends. Two packagings were observed on 2026-11-23:

- `17f1e52ea91854d8c6c266487597bf1dd6ce63441bfe27c9e61f64e2768440c7`
- `8083c1ee3d64d34b2ce5339bfca691b5c46cc675e51a938a64cb7c7eb9eab5e5`

Section 4 hashes the two extracted tables and stops if either differs from the values above.

The rule-set fingerprint is the SHA-256 of the classifier's pattern strings. It identifies the rules, not the normalization code. The release tag identifies the code.

## How to reproduce

Tested with Python 3.14.7, pandas 3.0.5, numpy 2.5.2, pyarrow 25.0.1 and matplotlib 3.11.2 on Windows.

```
python -m venv .venv
.venv\Scripts\activate            (Windows; elsewhere: source .venv/bin/activate)
pip install pandas==3.0.5 numpy==2.5.2 pyarrow==25.0.1 matplotlib==3.11.2 jupyterlab
jupyter lab Red3_Complete_Pipeline.ipynb
```

Then choose **Restart Kernel and Run All Cells**.

- **Download.** The first run downloads about 452 MB and extracts 1.3 GB into `data/`.
- **Caching.** The classified table is cached in `data/processed/`. Later runs reuse it unless `REBUILD_AUDIT = True`.
- **Outputs.** Every run writes a new `outputs/consolidated_runs/run_<UTC time>_<id>/` folder and never overwrites an earlier one.
- **Memory and time.** Section 18 needs more than 6 GB of free memory. With the classified table already cached, a full run of the remaining sections took about 68 minutes on the author's Windows machine (16 GB RAM); a first run adds the download and the one-off classification pass (roughly 40 minutes more).
- **Evaluation.** Section 26 scores the classifier only when the annotation file in `data/annotations/red3_consolidated/` covers every statement in the sample drawn by the run. The sample is deterministic for a given release and `RANDOM_SEED`. If Section 26 reports the annotation as incomplete, compare your `stratified_review_sample.csv` with the one stored here. The sample has not been tested for identical output on another operating system.

Settings in Section 1 used for these results:

| Setting | Value | Meaning |
|---|---|---|
| `RELEASE_DATE` | `2026-10-29` | USDA release analysed |
| `ANALYSIS_DATE` | `2026-11-21` | fixed reference date for record age |
| `RANDOM_SEED` | `42` | sampling seed |
| `NEGATIVE_SAMPLE_SIZE` | `300` | records sampled from `not_detected` |
| `CONTEXT_WINDOW` | `100` | characters of context kept around each match |
| `FORMULATION_QUEUE_CAP` | `500` | largest formulation groups exported for review |
| `ENABLE_CANONICAL_GROUPING`, `RUN_LLM`, `SAVE_ALIAS_TABLE` | `False` | optional sections, not used |

The pipeline is rule-based. With these settings it calls no language model.

## Method in brief

**Four statuses.** Every record receives one status from its ingredient statement.

| Status | Rule |
|---|---|
| `detected` | A numeric name (Red 3, Red No. 3, Red #3, Red Dye 3, with or without an FD&C, D&C or Food prefix) or the word "erythrosine" is present, and no review reason applies. |
| `review` | An alias or unfamiliar spelling is present (E127, INS 127, CI 45430, Food Red 14, Acid Red 51, tetraiodofluorescein, "erythrosin", "Red three"), or a match sits next to absence wording, or the statement contains conditional wording ("may contain", "may also contain", "if decorated", "if the product contains"). |
| `not_detected` | The statement is interpretable and contains no match and no review reason. |
| `unknown` | The statement is missing or is a placeholder such as "N/A". |

**Normalization.** Matching runs on text with HTML entities unescaped, Unicode compatibility forms and accents folded, case folded and whitespace collapsed. Every match is mapped back to character offsets in the original statement, so reported spans quote what the supplier wrote.

**Three units of analysis.**

1. *Source record.* One row of `branded_food.csv`.
2. *Latest eligible U.S. barcode.* For each barcode of 8, 12, 13 or 14 digits whose primary market is the United States, the record with the latest `publication_date`. Barcodes are used exactly as supplied. If the latest date holds records that disagree, the barcode is held for review.
3. *Product identity and declared formulation variant.* Records are grouped across the whole file, with no date filter. A product identity is defined by normalized owner, brand, sub-brand and description with package-size text removed. A formulation variant adds the full ingredient statement. Records that lack a description, an ingredient statement, or both brand and owner are kept as singletons.

**Component ablation (Section 24).** A substring pre-filter sends 1,165,852 records (58.0%) straight to `not_detected`. The patterns evaluate the other 838,986 records with a statement. On this release, normalization changed no outcome: every match was also present in the raw text. The absence-wording rules did not fire, and Section 13 finds no mention preceded by absence wording. All 1,035 review records come from conditional wording (927), an alias (118), or both (10). These components are safeguards that were implemented and tested; they are not findings.

## Validation sample

`processed/gold_standard_benchmark.parquet` carries the labels below onto 18,932 records. The file name is historical. The sample measures this classifier's error. It is not a hard test set, because every positive in it was found by the classifier and the negatives are a random draw.

**Design.** Statements are sampled, not records, because many records share one statement. Each stratum's estimate is weighted back to the eligible U.S. records it represents.

| Stratum | Sampling unit | Population | Sampled | Statements annotated | Eligible records represented |
|---|---|---:|---:|---:|---:|
| detected, erythrosine only | unique statement | 3 | 3 | 3 | 9 |
| detected, erythrosine and numeric name | unique statement | 17 | 17 | 17 | 62 |
| detected, numeric name | unique statement | 8,591 | 150 | 150 | 35,784 |
| review, all statements | unique statement | 247 | 247 | 247 | 1,026 |
| not_detected, random sample | record | 1,921,334 | 300 | 299 | 1,921,334 |

**Annotation.** Two annotators, one researcher and one non-specialist (both research acquaintances of the author, unpaid volunteers), independently labelled the 716 statements as `declared`, `not_declared` or `unclear`. Each worked from `annotation_sheet_blind.csv`, which shows the statement text without the classifier's status, and neither saw the other's labels. Annotation took about six hours for the first annotator and five for the second. Both read each statement in full rather than searching for colour terms.

**Agreement.** The annotators agreed on 713 of 716 statements (99.58%). In the three disagreements one annotator chose `unclear` and the other a definite label. No statement received opposing definite labels. The three cases were resolved in a joint adjudication session between the author (who also wrote the classifier) and the annotators; during that session, annotator 1 revised their own label on one statement (S00524) after re-reading its market-exception parenthetical. The stored labels and agreement figures reflect that revision. All three cases are listed, with the adjudicator's reasons, in `tables/adjudication_log.csv`.

| Scope | Statements | Raw agreement | Cohen's kappa |
|---|---:|---:|---:|
| All | 716 | 99.58% | 0.99 |
| `detected` | 170 | 100.00% | undefined |
| `not_detected` | 299 | 99.67% | 0.00 |
| `review` | 247 | 99.19% | 0.60 |

The overall kappa is high because the strata are easy to tell apart. Within a stratum nearly every statement has the same label, so kappa is low or undefined there and raw agreement is the figure to read.

**Labels.** 415 statements are `declared`, 300 `not_declared` and 1 `unclear`. All 170 `detected` statements were labelled `declared`. Of the 247 `review` statements, 245 were labelled `declared`, 1 `not_declared` (an erythrosine mention inside a market-exception parenthetical whose U.S. formulation declares Red 40 lake instead) and 1 `unclear`. None of the 299 sampled negatives was labelled `declared`.

**Accuracy, weighted to eligible U.S. records (95% intervals).**

| Operating point | Precision | Recall | F1 |
|---|---|---|---|
| Strict: `detected` only | 1.000 (0.945 to 1.000) | 0.972 (0.572 to 0.972) | 0.986 (0.713 to 0.986) |
| Flag-all: `detected` or `review` | 0.9999 (0.9461 to 0.9999) | 1.000 (0.590 to 1.000) | 0.9999 (0.726 to 0.9999) |

- **Strict recall** is below 1 because the 1,026 review records are counted as missed when only `detected` is accepted.
- **The recall interval is wide.** No declaration was found among 300 sampled negatives, but those 300 stand for 1,921,334 records. The sample cannot rule out a declared share of up to 1.26% among the negatives, which is about 24,000 records.
- **Estimated declared records:** 36,877 (34,892 to 61,168), or 1.88% (1.78% to 3.12%) of interpretable eligible records.

**Development and test partitions.** Statements are assigned by a hash of the statement text: 155 statements (4,678 records) to `dev` and 561 statements (14,254 records) to `test`. No statement appears in both. Near-duplicate statements, such as the same product in two sizes, can.

## Output files

Every table carries a `run_id` column. Row counts are for the run named above.

| File | Rows | Content |
|---|---:|---|
| `tables/audit_qc.json` | – | cohort counts, reconciliation checks, provenance, validation state |
| `processed/gold_standard_benchmark.parquet` | 18,932 | sampled statements' records with adjudicated label and partition |
| `tables/stratified_review_sample.csv` | 716 | the sampled statements with stratum and weights |
| `tables/annotation_sheet_blind.csv` | 716 | the sheet the annotators used |
| `tables/annotation_stratum_design.csv` | 5 | the sampling design |
| `tables/annotation_agreement.csv` | 4 | agreement by scope |
| `tables/adjudication_log.csv` | 3 | statements the annotators labelled differently, with the adjudicator's reasons |
| `tables/gold_stratum_estimates.csv` | 5 | per-stratum estimates and intervals |
| `tables/gold_evaluation_summary.csv` | 20 | precision, recall, F1 and population estimates |
| `tables/unique_text_annotation_queue.csv` | 9,157 | every distinct candidate and sampled negative statement |
| `tables/nlp_ablation_summary.csv` | 36 | routing funnel and component ablation |
| `tables/ingredient_position_summary.csv` | 44 | where in the statement the declaration sits |
| `tables/mentions.csv` | 39,834 | one row per matched name, with offsets and context |
| `tables/match_prefix_tokens.csv`, `tables/match_suffix_tokens.csv` | 25 each | most frequent two-word contexts immediately before and after a match |
| `tables/category_summary.csv` | 257 | status counts by category, with each category's denominator |
| `tables/owner_summary.csv` | 1,437 | detected records, variants and barcodes by brand owner |
| `tables/co_additive_summary.csv` | 9 | nine other colour additives (Red 40, Yellow 5, Yellow 6, Blue 1, Blue 2, Green 3, titanium dioxide, carmine, beet colour) declared in detected records |
| `tables/freshness_by_cohort.csv`, `tables/freshness_distortion.csv` | 25, 5 | record age by cohort, and how the record-level age profile differs from the barcode-level one |
| `tables/date_quality.csv` | 4 | completeness of the four date fields |
| `tables/formulation_size_distribution.csv` | 68 | records per formulation variant |
| `tables/successor_candidates.csv` | 1,831 | same product identity under a different barcode and formulation; a review queue, not a finding |
| `tables/candidate_manual_review.csv` | 36,881 | every eligible detected or review record |
| `tables/negative_manual_review_sample.csv` | 300 | the sampled `not_detected` records |
| `tables/chronology_review_queue.csv` | 27 | barcodes with conflicting latest records |
| `tables/formulation_review_queue.csv` | 500 | largest mixed formulation groups |
| `tables/review_sampling_design.csv` | 4 | how each review queue was selected |
| `figures/record_freshness_and_category.png` | – | record age and category profile |

The notebook also writes `processed/records_annotated.parquet` (2,010,212 rows), `processed/latest_by_gtin.parquet` (455,546 rows) and `processed/declaration_transitions.parquet` (390,030 rows). These three files are not included in the release; the notebook regenerates them from the source archive.

## Limitations

- **Records, not products on shelves.** Each row is a database record submitted voluntarily by a manufacturer or data aggregator. The database is not a census of the U.S. food supply and carries no sales weights.
- **Most records are old.** 81.57% of source records were published more than 48 months before the analysis date. Counts by barcode describe the database, not the current market.
- **Dates are database dates.** `publication_date` records ingestion by USDA. `modified_date` and `discontinued_date` are supplier-reported, and only 3,980 records (0.198%) carry a discontinued date. None is a manufacturing, reformulation or expiry date.
- **Identity is operational.** Barcodes are matched as supplied, without check-digit validation or padding. Formulation grouping can split identical products described in different words and can merge distinct ones.
- **Composite products.** Bracketed sub-components are parsed. Colon-delimited sub-components and statements with unbalanced brackets are not.
- **The validation sample has no hard cases.** It contains no statement that the classifier missed, so it cannot show what such a statement looks like. Misspellings and names in other languages that contain none of the candidate substrings would be missed by the classifier and are unlikely to appear in a random sample of 300.
- **Recall is bounded loosely.** See the interval above.
- **Annotators.** Two annotators, one of them a non-specialist, and an adjudicator who wrote the classifier.
- **One release.** The audit is a single snapshot and makes no causal claim about any regulatory event.

## Regulatory context

FDA revoked the authorization for FD&C Red No. 3 in food and ingested drugs by an order published on 16 January 2025. Manufacturers of food have until **15 January 2027** to reformulate, and manufacturers of ingested drugs until **18 January 2028**. FDA lifted an administrative stay of the order on 5 August 2026 (91 FR 50475) and confirmed those dates. Products made before the compliance date may remain in commerce, so a declaration in this database does not establish non-compliance.

- [FDA: FD&C Red No. 3](https://www.fda.gov/industry/color-additives/fdc-red-no-3)
- [Federal Register notice of 5 August 2026, 91 FR 50475](https://www.federalregister.gov/documents/2026/08/05/2026-15920/micro-tracers-inc-response-to-objections-and-requests-for-a-public-hearing)

## License and citation

- **Source data.** USDA FoodData Central data are in the public domain under CC0 1.0. USDA asks that FoodData Central be credited as the source: U.S. Department of Agriculture, Agricultural Research Service. FoodData Central, Branded Foods, release of 29 October 2026. https://fdc.nal.usda.gov/
- **Code.** MIT (see `LICENSE`).
- **Tables, labels and documentation.** CC BY 4.0.

To cite this release:

> Regmi, S. (2026). *Red3-FDC-2026: An Offset-Preserving, Deduplicated Dataset of FD&C Red No. 3 Declarations in U.S. Branded Foods, with Human-Adjudicated Validation Labels* (Version 1.0.0) [Data set]. Zenodo. https://doi.org/10.5281/zenodo.24488536

## Acknowledgements and contact

The two annotators, who remain anonymous at their preference, are thanked for their careful work.

Contact: Savyata Regmi · savyata2024@gmail.com · ORCID: [0009-0006-7736-9967](https://orcid.org/0009-0006-7736-9967)
