# First Findings – Data Analyst Job Market

*Results of the pipeline run of 2026-11-06 (`jobsuche_extract.py`, `build_dataset.py`, `build_trends.py`, `indeed_trends.py`, `adzuna_history.py`, `load_to_postgres.py`). Live postings cover 2026-10-04 → 2026-11-05: a one-month snapshot. Every number below can be re-computed from the SQL views in `sql/03_views.sql` or seen in the Power BI report (`powerbi/`) and the Streamlit app (`app/`).*

**What changed in this run**
- **Germany now includes Jobsuche** (Bundesagentur für Arbeit): 157 → **300 postings**, 31 → **180 full descriptions**, 6 → **31 stated salaries**. Germany is no longer the thinnest sample for the class's first target market.
- **France, Italy, Spain and Poland are reported on their own**; "Other EU" now means Austria, Belgium and Ireland.
- Role rule fixed for German plurals ("Data Analysten / Data Analystinnen", "(Data) Analyst").

**Storyline (one Power BI page each)**
1. How has the Data Analyst role evolved (2020–2026)? – *1a demand, 1b skills and work*
2. Which tools and skills are in demand today?
3. Which industries are hiring in each target country?
4. What work arrangements, languages and salaries do companies offer?
5. What does this mean for each student? (profiles from the class survey)

---

## 0. Data

| Step | Postings |
|---|---|
| Raw rows (Adzuna 3,292 · JSearch 5,561 · Jobsuche 1,589 active in the window · ITJobs and Arbeitnow analytics-related) | 9,371 after keeping analytics-related titles |
| Unique postings (reposts of the same title + company + country collapsed) | 5,190 |
| Known country, training ads and rating gigs excluded | 5,134 |
| **Analysis base: Core Data Analyst + BI Analyst** | **3,371** |
| of which with a full description (≥ 1,000 characters, used for skills) | 1,186 |
| of which placed on the map (city level) | 2,611 (77%) |

| Country group | Postings | With full description |
|---|---|---|
| Germany | 300 | 180 |
| Netherlands | 115 | 64 |
| Switzerland | 69 | 26 |
| Portugal | 229 | 92 |
| United Kingdom | 409 | 121 |
| USA | 839 | 284 |
| Canada | 158 | 21 |
| France | 252 | 42 |
| Italy | 178 | 42 |
| Spain | 258 | 78 |
| Poland | 152 | 87 |
| Other EU (AT, BE, IE) | 117 | 88 |
| Latin America (BR, MX) | 295 | 61 |

- **Jobsuche** (Bundesagentur für Arbeit, community-documented JSON interface, 699 detail calls, 0 failures): 1,809 German postings found by 5 queries, **1,589 active in the snapshot window** (published or updated since 4 October; 220 older ones excluded, like the other sources' one-month window). 155 Core Data Analyst / BI Analyst postings enter the analysis base, **97% with a full description** (median 3,240 characters). It also gives a home-office flag, a temp / placement agency flag and, for 1 posting in 10, the salary range stated by the employer.
- **Long-open roles in Germany:** 412 of the 1,589 active Jobsuche postings (26%) were first published before 4 October and are still open, some since 2024 – a sign of hard-to-fill positions.
- **JSearch** (Google Jobs, 941 credits) brought most other full descriptions. Deep result pages repeat the same postings under new ids: the extractor counts a posting as new only on title + company.
- **Adzuna** gives volumes, work mode when stated, language and salary, but only ~500-character snippets: snippets hide skills (SQL appears in 15% of snippets vs ~70% of full descriptions), so **skill shares use full descriptions only**.
- **Switzerland and Canada remain the thinnest samples** for skills (26 and 21 full descriptions): read their skill shares as indicative.

---

## 1a. Demand 2020–2026: the boom and the cool-down

*Indeed Hiring Lab job postings index (CC BY 4.0), 100 = 1 February 2020, seasonally adjusted. An index compares a country with itself over time, not levels between countries. The "Data & Analytics" sector exists for DE, UK, USA and Canada only.*

| Change from the 2022 peak to Oct 2026 | Data & Analytics | Software Development | All postings |
|---|---|---|---|
| United Kingdom | **−73%** (index 50) | −66% | −59% |
| USA | **−68%** (63) | −65% | −35% |
| Canada | **−64%** (88) | −67% | −40% |
| Germany | **−60%** (74) | −71% | −40% |

- Data & Analytics postings **almost doubled by 2022** (peak index 185–244), then fell **below their pre-pandemic level** in every country.
- The fall is **steeper than the whole job market** (−35% to −59%) and **similar to software development**: a tech-wide cool-down, not a Data Analyst-only problem.
- The whole market is back to or above 2020 levels in most countries (Germany 103, USA 104, Netherlands 113, Spain 144), the UK is the exception (69).
- **For the class:** demand is real but more competitive than in 2021–2022; the skills in section 2 are what makes the difference.

---

## 1b. How the skills evolved (2020–2026)

*Consistent geography (USA), same skill dictionary on every full description. Sources: Glassdoor ~2020 (n = 1,565), Kaggle Skills Evolution 2022–2026 (LinkedIn-based), LinkedIn April 2024 (n = 572). Last two columns: our live 2026 sample, USA only (n = 284) and all countries (n = 1,037).*

| Skill (% of postings) | 2020 | 2022 | 2023 | 2024 | 2025 | 2026 | Live 2026 USA | Live 2026 all |
|---|---|---|---|---|---|---|---|---|
| SQL | 68 | 58 | 67 | 65 | 87 | 82 | 70 | 71 |
| Python | 32 | 25 | 36 | 32 | 57 | 53 | 43 | 45 |
| Power BI | 13 | 29 | 34 | 40 | 48 | 49 | 55 | 60 |
| Tableau | 31 | 35 | 26 | 32 | 56 | 55 | 43 | 32 |
| Excel | 48 | 56 | 38 | 37 | 44 | 43 | 37 | 35 |
| Looker | 7 | 3 | 3 | 4 | 17 | 20 | 8 | 10 |
| Snowflake | 2 | 9 | 2 | 7 | 11 | 15 | 14 | 11 |
| dbt | 0 | 0 | 2 | 1 | 4 | 10 | 5 | 6 |
| Generative AI / LLM | 0 | 0 | 0 | 1 | 3 | 4 | 10 | 8 |

- **SQL has always been the foundation**: 7–8 postings out of 10.
- **Python went from a nice-to-have to a standard requirement**: about 1 in 3 postings until 2024, about 1 in 2 since 2025.
- **Power BI quadrupled** (13% → 49–55% in the USA) and leads outside the USA (60% overall vs Tableau 32%).
- **The modern data stack is entering the analyst role**: Snowflake (2% → 14%), dbt (0% → 5–10%), Looker (7% → 20% in Skills Evolution 2026).
- **GenAI is still early but rising**: 4% (Skills Evolution 2026) to 10% (our USA 2026 sample).
- **Cross-check:** our live USA sample is close to the independent Skills Evolution 2026 data (Power BI 55% vs 49%, Python 43% vs 53%), which supports the method.
- **Experience required is stable**: median 3 years in almost every period (2 in Skills Evolution 2024); 30–58% of postings accept ≤ 2 years (31% in our live 2026 sample).
- **Return to office** (ds_salaries, self-reported, Data Analyst / BI): fully remote 69% (2021) → 26% (2024).

**Validation:** on 853 Skills Evolution full descriptions, our regex extractor matches the dataset's LLM-extracted tags with ≥ 96% precision and ≥ 94% recall for SQL, Python, Excel, Power BI, Tableau, Looker, Snowflake, dbt, AWS and Azure. *Statistics* is over-detected (precision 51%) and reported with caution.

---

---

## 2. Tools and skills in demand today (full descriptions, % of postings)

| Skill | DE (180) | NL (64) | CH (26) | PT (92) | UK (121) | USA (284) | CA (21) | FR (42) | IT (42) | ES (78) | PL (87) | Other EU (88) | LatAm (61) |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SQL | 59 | 69 | 65 | 78 | 73 | 70 | 90 | 64 | 67 | 74 | 76 | 74 | 64 |
| Power BI | 43 | 73 | 50 | 79 | 50 | 55 | 67 | 55 | 67 | 56 | 63 | 62 | 59 |
| Python | 31 | 30 | 54 | 47 | 47 | 43 | 48 | 45 | 52 | 56 | 46 | 43 | 44 |
| Tableau | 17 | 17 | 4 | 25 | 33 | 43 | 52 | 48 | 24 | 24 | 25 | 18 | 34 |
| Excel | 28 | 19 | 8 | 30 | 42 | 37 | 48 | 29 | 52 | 29 | 45 | 25 | 43 |
| Azure | 12 | 20 | 15 | 27 | 8 | 17 | 14 | 5 | 12 | 17 | 18 | 17 | 15 |
| Snowflake | 8 | 5 | 4 | 18 | 12 | 14 | 10 | 17 | 7 | 10 | 8 | 9 | 7 |
| GenAI / LLM | 5 | 5 | 4 | 5 | 6 | 10 | 5 | 0 | 12 | 14 | 14 | 6 | 10 |

- **SQL + Power BI is the core stack in every market**; Power BI leads Tableau everywhere (France is the closest: 55% vs 48%).
- **Germany, now on 180 descriptions (95% interval about ±7 points):** SQL 59%, Power BI 43%, Python 31%, Tableau only 17%. The earlier Germany figure for Tableau (39% on 29 descriptions) was sampling noise. **SAP is a German speciality**: SAP ERP in 21% and SAP BI / Analytics Cloud in 10% of German descriptions.
- Python is asked in 30–56% of postings: highest in Spain and Switzerland, lowest in Germany and the Netherlands.
- Portugal shows the most "modern stack" (Azure 27%, Snowflake 18%); GenAI / LLM is highest in Spain and Poland (14%).
- Samples below 30 full descriptions (Switzerland, Canada) are flagged in the Power BI report and in the app.

---

## Where are the jobs? (map)

- Cities are located offline with the GeoNames gazetteer; city districts are merged into their city (Mitte → Berlin); regions and "anywhere" are not placed. Source coordinates are used only when consistent.
- **Top cities:** London 122, Lisbon 91, Paris 90, Madrid 76, New York 75, Barcelona 73, Warsaw 62, Manchester 46, Washington 46.
- **Germany is spread out** (no single dominant city): Hamburg 31, Munich 23, Berlin 21, Frankfurt 14, Düsseldorf 9, Stuttgart 7.
- Stand-alone versions for the README and the slides: `docs/map.html` (interactive) and `docs/map.png`.

---

## 3. Industries (approximate: company name + description keywords)

- Industry is identified for 37–70% of postings depending on the country (higher with a full description).
- **Recruitment and temp agencies post a large share** in Switzerland (25%), the Netherlands (23%), the UK (20%) and Germany (16%; 25% of Jobsuche postings are temp or placement agencies). They are shown separately and excluded from the "top employers".
- Main hiring industries (direct employers): Germany – Banking & Financial Services, Healthcare & Pharma, Consulting & IT; UK – Banking, Insurance; Switzerland – Insurance, Banking; Portugal – Consulting & IT Services; USA – Healthcare & Pharma, Technology; Netherlands – Consulting, Public Sector; France – Manufacturing, Insurance, Banking; Italy – Consulting & IT, Technology; Spain – Technology, Gaming, Consulting; Poland – Banking & Financial Services.
- ITJobs (Portugal's main IT job board) lists 809 active IT postings but only 13 Data Analyst / BI roles: data jobs there are mostly Data Engineer and Business Analyst positions.

---

## 4. Work mode, language, seniority and salary

**Work mode** (among postings that state one):

| | Hybrid | Remote | On-site | n stated | not stated |
|---|---|---|---|---|---|
| Germany | 83% | 11% | 6% | 112 | 63% |
| Netherlands | 91% | 5% | 5% | 43 | 63% |
| Switzerland | 74% | 26% | 0% | 19 | 72% |
| Portugal | 60% | 34% | 6% | 125 | 45% |
| United Kingdom | 78% | 14% | 8% | 192 | 53% |
| USA | 42% | 33% | 24% | 362 | 57% |
| Canada | 45% | 45% | 9% | 53 | 66% |
| France | 58% | 29% | 13% | 31 | 88% |
| Italy | 41% | 53% | 6% | 49 | 72% |
| Spain | 49% | 46% | 5% | 94 | 64% |
| Poland | 63% | 22% | 14% | 49 | 68% |

- **Hybrid is the norm in Northern Europe** (Germany, Netherlands, UK, Switzerland: 74–91%); **Southern Europe is more remote-friendly** (Italy 53%, Spain 46% remote). The USA is the most on-site market.
- Germany: Jobsuche's home-office flag lifts the share of postings that state a work mode (82% → 63% not stated). In Germany home office almost always means hybrid.
- **Caveat:** postings that say nothing are probably mostly on-site, so the on-site share is under-estimated everywhere. France states a work mode in only 12% of postings: read it as indicative.

**Language – the hidden barrier** (not applicable to the UK, USA and Canada):

| | Posting written in English | Local language needed |
|---|---|---|
| France | 11% | 89% |
| Germany | 13% | 88% (76% without Jobsuche) |
| Netherlands | 17% | 81% |
| Italy | 23% | 69% |
| Switzerland | 33% | 57% |
| Other EU (AT, BE, IE) | 47% | 53% |
| Spain | 62% | 39% |
| Poland | 68% | 34% |
| Portugal | 70% | 29% |

- **About 9 in 10 Data Analyst postings in France and Germany need the local language**, 8 in 10 in the Netherlands: the most actionable finding for an international class.
- **Portugal, Poland and Spain are the most English-friendly markets** in continental Europe.
- **Source effect in Germany:** Jobsuche is a German-language board (99% of its postings need German). Without it, Germany would read 76%. The truth for an English-speaking job seeker is in between: international boards over-represent English postings, the national board shows the full market. The dashboard shows both (page 4 toggle).

**Seniority:** 41–68% of postings state neither a level in the title nor years of experience. Explicitly junior / intern postings are 4–23% in most markets, with one exception: **France 38%**, driven by *stage* and *alternance* (internship / work-study) postings – a real entry route for graduates willing to work in French.

**Salary – average advertised salary for "data analyst", last 12 months** (Adzuna history endpoint, gross per year, converted with ECB monthly reference rates; page 4b):

| Country | Local currency | EUR |
|---|---|---|
| USA | 119,800 USD | 103,500 |
| Canada | 108,500 CAD | 67,500 |
| United Kingdom | 56,800 GBP | 65,800 |
| Netherlands | 53,700 EUR | 53,700 |
| Belgium | 43,300 EUR | 43,300 |
| Italy | 39,600 EUR | 39,600 |

- **Germany (new): median €61,000** on 31 stated annual salaries (25th–75th percentile €55,000–75,000), 25 of them salary ranges stated by the employer on Jobsuche. Not comparable one-to-one with the Adzuna averages above (median vs mean, different postings), but the first usable German figure.
- An average is pulled up by senior roles: the **median of our own live postings is 10–25% lower** (UK £45,000 on 139 postings, USA $100,000 on 113).
- **Spain: not representative.** The 92 Spanish postings with a stated salary are mostly senior / international remote roles (median €80,000); do not quote it as the Spanish market.
- **Not shown:** Switzerland, France, Spain, Austria, Brazil, Mexico (no Adzuna salary history); Portugal (not covered by Adzuna); Poland (Adzuna mixes daily / monthly B2B rates in as annual).
- The Adzuna history covers only 12 months and is too noisy for a trend: it is used as a cross-country comparison only.

---

## 5. Student profiles

- Each student's profile is computed on **all** target countries (named ones and those included via "EU" / "Latin America"); with France, Italy, Spain and Poland now separate, the five students who wrote "EU" see each of these markets on its own line.
- Profiles show matching postings, hybrid share, local-language need, explicit junior share, top skills and top hiring industry, with a warning below 30 full descriptions.

---

## Limitations

- One-month snapshot; source coverage differs by country. Switzerland is under-represented compared with its real market size.
- **Germany mixes international boards and Jobsuche**, a German-language national board: language and work-mode shares depend on the source mix (shown with and without Jobsuche). Jobsuche is a community-documented interface, not an official API, and may change.
- Skill trend 2020–2026 is US-only; the demand trend (Indeed) covers DE, UK, USA, Canada for Data & Analytics (France, Italy, Spain only for all postings).
- Rule-based classifiers (role, seniority, work mode, language, industry): checked on samples and, for skills, against LLM tags, but there is no full ground truth.
- Duplicates are collapsed per title + company + country, so multi-city openings count once.
  **Robustness check (near-duplicates):** a TF-IDF similarity test (title, company, description; ~92% precision on a manual review of 40 pairs) finds 196 further reposts (5.8% of the base, up to 9.5% in the UK, mostly Adzuna). Removing them changes no skill, work-mode, industry or seniority share by more than 1 point (Switzerland, n = 26: up to 2.6 points), so they are kept in the base and the findings are robust to them. Details: `docs/near_duplicates_check.md`.
- Salaries are advertised (partly estimated by Adzuna) and selection-biased, not paid; cost of living is not taken into account.

## Sources and attributions

Adzuna API ("Job data: Adzuna") · JSearch / Google Jobs (RapidAPI) · Jobsuche, Bundesagentur für Arbeit (community documentation: github.com/bundesAPI/jobsuche-api) · ITJobs.pt API · Arbeitnow API · Indeed Hiring Lab Job Postings Index (CC BY 4.0) · ECB Data Portal exchange rates · Kaggle: Glassdoor Data Analyst Jobs, LinkedIn Job Postings 2023–2024, Skills Evolution, ds_salaries · GeoNames (geonamescache) · Natural Earth (world-atlas).
