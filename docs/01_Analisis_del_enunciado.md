# Phase 1-2 · Analysis of the professor's assignment

> Written for the student. The deliverables for the professor (notebook, presentation, report) are in Spanish because all the course material is in Spanish.

## 1. What the uploaded files actually say

| File | What it is | Role in the project |
|---|---|---|
| `Caso_Practico_NLP_Glassdoor_RRHH.pdf` (5 pages, Evolve Academy, Máster en Data Science & IA) | **The assignment statement.** Business context, dataset, expected process (3.1-3.7), deliverables (4), grading criteria (5), references (6). | Source of every mandatory requirement. |
| `Glassdoor_ejemplo.ipynb` | **Professor's skeleton notebook** (Deutsche Bank vs. 4 banks). Only section 0 (loading, sampling, variable preparation) is implemented; sections 1-7 are headings with hints. | Shows the expected notebook structure and coding style (Spanish comments, `SEED=42`, `MAX_POR_EMPRESA`, Parquet filter). |
| `Analisis de reseñas (plantilla)2.pdf` (7 slides) | **Optional presentation template**: Objetivos / Asunciones, Insights, Validación sentimiento Pros y Cons, Análisis topics, Conclusiones y Próximos pasos. | Structure of the presentation (template use is optional, but it tells us what the professor expects to see). |
| `lista_con_sector.xlsx` | 2,796 companies (≥300 reviews) with review counts and a sector label (auto-assigned, "revisar filas A verificar"). | Used to choose a company and peers of the same sector. |
| `glassdoor_reviews_hr.parquet` | 5,004,045 reviews, 2,796 firms, 2018-07-27 → 2023-07-10, 7 columns (`firm, date_review, current, overall_rating, recommend, pros, cons`), no nulls. | The dataset (prepared by the professor from Gauthier's two Kaggle datasets). |

### Important: the title "Tree of Models NLP" does not appear anywhere

None of the uploaded files mention a "tree of models", a model taxonomy or a hierarchy of NLP techniques. The actual assignment title is:

**"Caso Práctico de NLP — Diagnóstico de Marca Empleadora con Reseñas de Glassdoor"** (NLP practical case: employer-brand diagnosis with Glassdoor reviews).

It is an applied, business-oriented NLP case: sentiment analysis + topic modeling on employee reviews, compared against competitors, with recommendations for an HR department. The "tree" idea most likely came from a generic prompt; it was **not** used as a requirement. The only "hierarchy" that exists in the assignment is the mapping *reviews → BERTopic topics → HR business categories*, which is implemented and documented.

## 2. Requirements, classified by source

### 2.1 Explicitly required by the professor (PDF, sections 2-5)

**Setup**
- Choose one company and 3-5 peers of the same sector and comparable size; justify in one or two sentences (peers must compete for the same talent).
- Optionally sample (e.g. 2,000 reviews per company, fixed seed) so that models run in reasonable time. Colab with GPU recommended.
- Load only the needed firms with the Parquet filter.

**3.1 Preparation and EDA**
- Split `current` into situation (current/former) and tenure.
- Per company: number of reviews, mean rating, % recommend, split current vs. former.
- Rating evolution over the time window.
- At least one actionable conclusion per EDA section.

**3.2 Sentiment on pros and cons (quality control)**
- Apply a pre-trained transformer (e.g. `distilbert-base-uncased-finetuned-sst-2-english`) to pros and cons separately, with `truncation=True` (512-token limit).
- Per company: % of pros classified positive and % of cons classified negative; most should match.
- Read a sample of discordant cases (positive cons, negative pros) and explain what kind of reviews they are.

**3.3 Topic modeling**
- BERTopic (or another technique seen in class, justified) on pros and, separately, on cons.
- **One single model** trained on the company + all peers together (one for pros, one for cons) — comparability rule.
- Decide what to do with outliers (reviews not assigned to any topic) and justify.
- Group topics into HR-interpretable business categories (e.g. compensation, work-life balance, management, career, culture) and review the assignments by hand.

**3.4 Comparison with peers**
- % of each company's reviews in each category, pros and cons; compare with peer average.
- Net balance per category (% pros − % cons) and gap vs. peer average (percentages are relative, hence the net balance).
- Contrast text conclusions with `overall_rating` and `recommend`: reviews complaining about a real problem should have worse ratings.

**3.5 Current vs. former employees**
- Compare cons categories of current vs. former employees of the company.
- Former employees' cons = exit reasons: compare with peers.
- Former employees rate lower everywhere (selection bias): compare topics, not sentiment.

**3.6 Improvement areas and recommendations**
- Prioritisation matrix: weight of each category in the company's cons vs. its disadvantage relative to peers.
- At most 3 concrete recommendations, written for a non-technical HR reader.
- Limitations: Glassdoor representativeness, mix of countries and areas, topic precision.

**4. Deliverables**
- One single clean, reproducible Jupyter Notebook (`.ipynb`) that runs top to bottom without errors, with code comments explaining decisions and a final conclusions cell with the main business findings.
- A presentation (ppt or similar) to be presented in class; a video if the student cannot attend.
- Deadline: one week after publication.

**5. Grading**
- Notebook 60 %: EDA, sentiment QC, topic modeling and labelling, peer comparison, current vs. former, quality of business insights.
- Presentation 40 %: business problem, company and peers chosen, strengths and improvement areas, proposed HR actions, limitations and next steps (other analyses or models).
- Top grade requires every technical decision (sentiment model, BERTopic parameters, outlier treatment, category grouping) to be justified from the **business** perspective, not only the methodological one.

### 2.2 Inferred from the course materials (not literally stated, but clearly expected)

- Language of the deliverables: Spanish (all materials, the skeleton notebook and the template are in Spanish).
- Notebook structure mirroring the professor's skeleton: 0 load/prep · 1 EDA · 2 sentiment · 3 topics · 4 peers · 5 current vs. former · 6/7 conclusions.
- Same conventions as the skeleton: `TARGET`, `PEERS`, `MAX_POR_EMPRESA`, `SEED = 42`, per-firm `sample(random_state=SEED)`, `recommend` mapping v/o/x.
- The skeleton allows VADER (lexicon) as an alternative sentiment model and mentions that some pre-trained models output a neutral class; the binary DistilBERT model is the default suggestion.
- Business labels suggested in the skeleton for topics: "Compensación y beneficios, Conciliación, Carrera y desarrollo, Management, Cultura, Burocracia y procesos, ...".
- Presentation content from the template: objectives, assumptions (filters, nulls, criteria), insights, sentiment validation, topic analysis, conclusions, action plan, next steps. Charts can be pasted from Python "pero que quede bonito".
- "Próximos pasos" should include other analyses or models to propose.

### 2.3 Optional (extra credit) and improvements

- **Explicitly optional (3.7, "suma nota")**: temporal evolution of the main categories (e.g. before/after 2020); analysis by tenure or by recommendation. → Both are implemented in section 7 of the notebook.
- **Not required, added for robustness**: sensitivity check of the outlier reassignment (section 4.1), cross-tab of sentiment vs. rating (section 2), PNG export of every chart for the slides, cache of expensive steps.
- **Not required by the professor**: a written academic report. The user's prompt asked for one; it is provided as `docs/02_Informe_academico.md` but the graded deliverables are the notebook and the presentation.

## 3. Interpretation decisions (provisional, clearly labelled)

| Ambiguity | Decision taken | Why |
|---|---|---|
| Which company? ("una empresa que eliges tú") | **Deloitte**, peers **PwC, EY, KPMG** (Big Four). | Same sector in `lista_con_sector.xlsx` ("Consultoría y servicios profesionales"), comparable size (25k-40k reviews), identical talent pool (graduate programmes, auditors, consultants, tech). Different from the professor's banking example. The notebook is parametric: changing `TARGET`/`PEERS` re-runs everything. |
| Sample size | 2,000 reviews per company (8,000 total) for the NLP models; the EDA uses all 129k reviews of the four firms. | The PDF suggests 2,000; EDA on everything is free and more precise. |
| "% que recomienda" denominator | Share of `v` over all reviews (including "sin opinión"); the share of `x` is also shown. | Most conservative reading; both numbers are reported. |
| Tenure buckets | Glassdoor labels "more than 1/3/5/8/10 years" mapped to 1-3, 3-5, 5-8, 8-10, >10 years. | Glassdoor buckets are lower bounds; stated as an assumption in the notebook. |
| Empty / meaningless texts ("none", "nothing", "no cons") | Detected with a regex before topic modeling and reported as the category *Sin contenido*; excluded from the net balance. | Forcing "nothing" into a topic would create noise; a "no cons" answer is itself a (positive) signal. |
| Outliers (BERTopic topic −1) | Reassigned to the nearest topic by embedding similarity; a robustness check shows the gaps keep their sign without the reassignment. | The comparison needs every substantive review to count; justified from the business side in the notebook. |
| Categories | 8 HR categories + "Otros" + "Sin contenido", shared by pros and cons so the net balance is computable. | Mirrors the labels suggested by the professor's skeleton. |
| Report format | Not required; Markdown report provided, presentation in `.pptx`. | The professor only grades notebook + presentation. |

## 4. Implementation plan (as executed)

1. Environment: Python 3.12 conda env (`glassdoor-nlp`) with torch 2.2.2 (last build for Intel Macs), transformers, sentence-transformers, BERTopic, UMAP, HDBSCAN. `requirements.txt` + Colab install cell.
2. Notebook section 0: parameters, Parquet filtered load, variable preparation, stratified-by-firm sample with seed 42, sanity check that the sample matches the population.
3. Section 1: EDA tables and charts (summary per firm, rating distribution, current vs. former, yearly evolution, volume), one actionable conclusion per block.
4. Section 2: DistilBERT SST-2 pipeline on pros and cons, QC table per firm, discordant examples, sentiment × rating cross-check, written diagnosis of the discordant types.
5. Section 3: empty-text detection, MiniLM embeddings, BERTopic (UMAP seed 42, HDBSCAN min_cluster_size 60), outlier policy, topic tables with representative reviews, manual topic → category mapping, manual review of 3 reviews per category.
6. Section 4: % per category per firm, target vs. peer average, net balance and gap, heatmap, robustness check, contrast with rating/recommend.
7. Section 5: current vs. former cons; exit reasons vs. peers; pros by situation.
8. Section 6: prioritisation matrix, strengths/weaknesses, ≤3 HR recommendations, limitations.
9. Section 7 (extra): categories by period, by recommendation and by tenure.
10. Section 8: conclusions cell + numeric summary printed from the data.
11. Execute the notebook top to bottom (fresh, no cache), export charts to `figuras/`, build the presentation from the real results, write the report, run the compliance checklist.
