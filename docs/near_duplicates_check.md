# Robustness check – pipeline rebuild and near-duplicate postings

*Run of 2026-11-09.*

## How to reproduce

```powershell
python src\check_near_duplicates.py     # writes data/processed/near_dup_pairs.csv and near_dup_groups.csv
```

The check was first run on an isolated copy of the pipeline (separate folder and database `job_market_test`), which was deleted after the results were applied to the project.

## Phase 0 – rebuild from raw

| File | Result |
|---|---|
| `jobs_all.csv`, `jobs_analysis.csv`, `job_skills.csv`, `trends_salary_remote.csv` | **identical** to the originals: the pipeline is reproducible |
| `trends_postings.csv`, `trends_skills.csv`, `trends_experience.csv` | **different**: the original trend files (06/11 08:57) were built *before* the last live-data rebuild (06/11 17:50) |

The original "Live snapshot 2026" trend line uses 1,037 full descriptions and the rebuilt one uses 1,186. Its skill shares move by up to 3 points (Stakeholder communication −3.0, Tableau −2.4, Power BI −2.1, Python −1.9, SQL −1.6). The trend files were then rebuilt in the project (`python src\build_trends.py`).

All quality checks are at 0 after the reload.

The `job_market` database (loaded on 05/11 with 1,911 postings) was reloaded with the current data (3,371).

## Near-duplicates (`src/check_near_duplicates.py`)

The exact key (normalised title + company + country) misses reposts with small variations. Method (scikit-learn, TF-IDF + cosine similarity, always within the same country):

- **title** similarity: character 3–5-grams
- **company** similarity: character n-grams after removing legal suffixes (GmbH, Inc., Ltd…)
- **head** similarity: first 500 characters of the description (the only part that Adzuna snippets share with the other sources)
- **full-text** similarity: only when both descriptions are full

| Rule | Meaning |
|---|---|
| A | same employer, title variant, similar opening text |
| B | same text posted by a different poster (agency, aggregator, other branch) |
| C | the exact-key logic made tolerant to spelling and legal suffixes |
| Guards | seniority words must match ("Junior BI Analyst" ≠ "BI Analyst"); two full descriptions must also agree as a whole, so shared company boilerplate is not enough |

### Manual review

| Version | Accepted pairs reviewed | Correct | Main error |
|---|---|---|---|
| v1 (rules A, B) | 42 (14 per rule) | rule A ≈ 65 %, rule B ≈ 93 % | Rule A merged different openings: Junior vs no tag, Sr vs no tag, different specialisations |
| **v2 (A, B, C + guards)** | **40 random** | **37 / 40 ≈ 92 %** | Same company boilerplate with a different specialisation (Aivancity "Alternance" ads, Thales internships) |

Recall: 100 pairs were rejected as "near miss" (31 because of different seniority). Some of them are real duplicates whose Adzuna snippet starts at a different point than the full description. The method is conservative: it prefers to keep a duplicate rather than delete a real posting.

### Effect on the analysis base

| | Before | After | Removed |
|---|---|---|---|
| Postings | 3,371 | 3,175 | 196 (5.8 %) |
| Full descriptions | 1,186 | 1,158 | 28 |
| Adzuna / JSearch / Jobsuche | 1,727 / 1,460 / 155 | 1,575 / 1,421 / 150 | 8.8 % / 2.7 % / 3.2 % |
| United Kingdom / Germany / Other EU | 409 / 300 / 957 | 370 / 276 / 890 | 9.5 % / 8.0 % / 7.0 % |

| Result | Before | After |
|---|---|---|
| Skill shares (full descriptions, all countries) | SQL 69.8 · Power BI 57.6 · Python 43.1 · Tableau 29.1 | SQL 69.7 · Power BI 57.8 · Python 42.8 · Tableau 28.9 (max change 0.25 pp) |
| Skill shares by country | | changes ≤ 1.1 pp, except Switzerland (n = 26 → 25): up to 2.6 pp |
| Work mode (when stated) | Hybrid 57.2 · Remote 29.7 · On-site 13.1 | 57.0 · 29.8 · 13.2 |
| Staffing agencies / industry known / junior + intern | 10.9 % / 55.2 % / 12.4 % | 10.8 % / 55.4 % / 12.2 % |

## Conclusions

1. **Duplicates inflate volumes, not percentages.** Removing them lowers the posting counts by ~6 % (up to ~10 % in the UK), mostly Adzuna reposts, but every share used in the findings moves by less than 1 point.
2. **The findings are robust to duplicates.** This is worth one sentence in the Methodology page.
3. **The trend files were stale**: they were rebuilt and the `job_market` database reloaded.

## Files

- `src/check_near_duplicates.py` – the check
- `data/processed/near_dup_pairs.csv` – every candidate pair with its scores, rule and an empty `is_duplicate_manual` column for further review
- `data/processed/near_dup_groups.csv` – posting → near-duplicate group and which posting would be kept
