# Education requirements in the 2026 postings

**Question:** do Data Analyst postings ask for a degree, at which level, and do they accept equivalent experience instead?

**Data:** the 1,186 live 2026 postings with a full description (analysis base). Snippets (Adzuna, ~500 characters) cut the requirements section, so they are left out, as for skills.

## Method

The rules were built **from the wording of our own postings**, not from a list written in advance:

1. **Structure.** Descriptions are flattened text (no line breaks). Requirements are bullet lists under headers such as *Requirements*, *Qualifications*, *Das bringen Sie mit*, *Dein Profil*, *Gevraagd profiel*, *Votre profil*, *Requisitos*. The bullets are "•" (JSearch), " - " (Jobsuche), "* " or "●". Each description is split into bullets and sentences.
2. **Vocabulary.** Every bullet with a broad "seed" word (degree, Studium, opleiding, licenciatura, laurea, Bac+…) was extracted and read, language by language. Typical formulas:

| Language | Typical formula |
|---|---|
| English | "Bachelor's degree in X, Y **or a related field**" + "required/preferred" + "**or equivalent experience**" |
| German | "**Abgeschlossenes Studium** der Informatik … **oder eine vergleichbare Qualifikation**"; "Ausbildung **oder** Studium" |
| Dutch | "**HBO/WO werk- en denkniveau**" (a thinking level, softer than a diploma) |
| French | "**Bac+5** (école d'ingénieur, école de commerce) ou équivalent" |
| Spanish / Portuguese / Italian / Polish | "Grado en / titulación", "Licenciatura em / formação superior", "Laurea (magistrale) in", "Wykształcenie wyższe" |

   False friends removed: *información* (contains "formación"), training offered as a benefit (*opleidingsbudget*, *formación continua*, *apprenticeship scheme*), Italian *in grado di* ("able to"), *master data*, *Scrum Master*, *degree of*, *risk degree*, *FP&A*, *University of …* as the employer.

3. **Fields** (rules in `src/taxonomy.py`, section 8; function `classify_education` in `src/build_dataset.py`):

| Column | Meaning |
|---|---|
| `education_level` | **minimum** level accepted = the lowest one mentioned: *Below degree* (vocational training, high-school diploma, associate degree) < *Bachelor* < *Master* < *PhD*; *Degree (level not stated)* when no level is explicit; *Not mentioned* |
| `education_mentioned` | a degree / qualification is mentioned |
| `education_required` | mentioned and not only "preferred" ("preferred", "a plus", "von Vorteil", "pluspunt", "se valorará"… within 60 characters of the mention) |
| `education_equivalent_ok` | "or equivalent experience", "vergleichbare Qualifikation", "gleichwertige Berufserfahrung", "Quereinsteiger" (career changers)… |
| `education_student` | currently enrolled (internships, working students) |
| `education_no_degree` | says explicitly that no degree is needed (very rare) |

JSearch has no structured education field in our data (`job_highlights` is empty in all 5,680 results), so the rules were validated by hand.

## Validation (by hand)

| Sample | Use | Result |
|---|---|---|
| 100 postings (40 en, 20 de, 10 nl, 8 es, 8 fr, 6 it, 4 pt, 4 pl) | **development**: errors found here were used to correct the rules | first version: 5 mentions missed, 1 false mention |
| 60 new postings, same language mix | **test**, rules not changed before measuring | mentions a degree: **precision 94 %** (30/32), **recall 100 %** (30/30 of the mentions found by the seed words) · minimum level correct **87 %** · required vs preferred correct **93 %** · equivalent experience correct **93 %** |

The errors of the test sample (*risk degree*, *FP&A*, a generic mention winning over an explicit "Bac+5" or "Laurea magistrale", "Bachelorstudium") were corrected afterwards; the corrected rules reproduce every case of both samples. A third, independent sample would be needed to measure the final version exactly.

**Limitations**
- "Not mentioned" ≠ "not required": a degree can be taken for granted.
- Recall is lower in the languages with few postings (Polish, Italian, Portuguese).
- *Required / preferred* and *equivalent experience* depend on bullet structure: reliable on the 2026 postings, not on the historical datasets (Glassdoor 2020 and LinkedIn 2024 have bullets in only 5–9 % of the descriptions), which is why the analysis covers 2026 only. On the historical datasets the simple "mentions a degree" share is stable in the USA: 72 % (2020), 73 % (2024), 75 % (2026).

## Results (2026, full descriptions) — `jm.v_education_by_market`

| Market | n | Mention a degree | Require it | Of these, accept equivalent experience | Min. Bachelor / degree | Min. Master / PhD | Min. below degree |
|---|---|---|---|---|---|---|---|
| **All countries** | 1,186 | **60 %** | 58 % | 29 % | 50 % | 5 % | 6 % |
| **Europe** | 820 | **56 %** | 54 % | **31 %** | 44 % | 7 % | 5 % |
| **North America** | 305 | **75 %** | 71 % | **27 %** | 67 % | 2 % | 7 % |
| Latin America | 61 | 46 % | 43 % | 4 % | 43 % | 0 % | 3 % |
| USA | 284 | 75 % | 71 % | 28 % | 67 % | 2 % | 7 % |
| Germany | 180 | 79 % | 77 % | 48 % | 63 % | 1 % | **16 %** |
| United Kingdom | 121 | **29 %** | 27 % | **51 %** | 23 % | 1 % | 5 % |
| Portugal | 92 | 52 % | 52 % | 8 % | 50 % | 2 % | 0 % |
| Other EU (AT, BE, IE) | 88 | 58 % | 58 % | 45 % | 38 % | 18 % | 2 % |
| Poland | 87 | 45 % | 40 % | 21 % | 43 % | 2 % | 0 % |
| Spain | 78 | 40 % | 37 % | 13 % | 30 % | 6 % | 4 % |
| Netherlands | 64 | 77 % | 75 % | 0 % | 64 % | 9 % | 3 % |
| France | 42 | 55 % | 55 % | 35 % | 21 % | **33 %** | 0 % |
| Italy | 42 | 52 % | 52 % | 9 % | 36 % | 17 % | 0 % |
| Switzerland ⚠ | 26 | 62 % | 62 % | 44 % | 50 % | 4 % | 8 % |
| Canada ⚠ | 21 | 71 % | 71 % | 13 % | 71 % | 0 % | 0 % |

⚠ fewer than 30 postings: indicative only.

**Reading**
- In North America 3 postings out of 4 mention a degree, in Europe just over 1 out of 2.
- The level asked is almost always a **Bachelor or an unspecified degree**. A Master as the minimum is common only in France ("Bac+5", 33 %), Italy ("laurea magistrale", 17 %) and Belgium.
- **Between a quarter and a third of the postings that mention a degree accept equivalent experience**: the space where a bootcamp with a strong portfolio can compete. The UK is the most open market (degree mentioned in 29 % of postings, half of them accept equivalent experience); Germany accepts "vergleichbare Qualifikation" in almost half of the cases and vocational training (*Ausbildung*) in 16 % of all postings.
