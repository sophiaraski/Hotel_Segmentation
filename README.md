# Hotel Customer Segmentation — Case 1 (Group R)

A formal, reproducible implementation of **Hotel H** customer segmentation, developed as part of the *Master Degree Program in Data Science and Advanced Analytics* (MDSAA) — Business Cases with Data Science, Case 1. The project follows the **CRISP‑DM** lifecycle and delivers actionable customer segments and marketing recommendations for an independent hotel chain operating in Lisbon.

> **Scope**: end‑to‑end notebook(s) and scripts for data understanding, preparation, modeling (K‑Means), evaluation, and business interpretation.

---

## 1) Executive Overview

This repository operationalizes the analysis described in the accompanying report and produces **data‑driven customer segments** to refine Hotel H’s marketing strategy. We integrate **behavioral, demographic, geographic, and preference** features to create interpretable segments and associated **business playbooks** (targeting, channels, offers).

**Key highlights**

- Dataset of **111,733 customer profiles** with booking behavior, stay metrics, and preferences (e.g., room requests, channels).  
- A **hybrid** segmentation approach: an *a priori* cluster for “services‑only” customers and **post hoc** K‑Means for the remaining population.  
- Robust preprocessing (deduplication, imputation, outlier handling, binning, encoding, scaling) and **dimensionality reduction with PCA**.  
- **Elbow method** (with *kneed*) to select *k* and **silhouette**-based validation.  
- **Actionable profiles** spanning youth/adventurers, diverse high spenders, senior and mature leisure segments, midlife explorers, and last‑minute visitors.

---

## 2) Project Structure

```
.
├── data/
│   ├── raw/                # (not tracked) original CSVs/Parquet (PII excluded or anonymized)
│   └── processed/          # (not tracked) intermediate artifacts
├── notebooks/
│   ├── 01_data_understanding.ipynb
│   ├── 02_preparation.ipynb
│   ├── 03_modeling_kmeans.ipynb
│   ├── 04_evaluation_and_profiles.ipynb
│   └── 05_business_actions.ipynb
├── src/
│   ├── features/           # transformations, binning, encoders, scalers
│   ├── modeling/           # kmeans, pca, selection, metrics
│   ├── utils/              # IO, plotting, config
│   └── pipelines/          # orchestration (e.g., sklearn Pipeline)
├── reports/
│   └── Report Group R - Case 1.pdf
├── requirements.txt
├── LICENSE
└── README.md
```

---

## 3) Methodology (CRISP‑DM Summary)

- **Business Understanding** — Improve segmentation beyond “sales origin” by integrating richer features; deliver segments for **targeted marketing** and **retention**.
- **Data Understanding** — Profiles include demographics (age, nationality), booking and stay behavior (lead time, PersonNights/RoomNights, cancellations, no‑shows), channels, and special requests (e.g., bed type, quiet room).  
- **Data Preparation**  
  - **Record linkage & deduplication** using `DocIDHash`, `NameHash`, `Nationality`; aggregation rules per feature (e.g., medians for age, sums for counts).  
  - **Invalids & outliers** removal (negative ages/lead times; extreme caps for revenue and booking counts).  
  - **Imputation** of missing *Age* via **KNN (k=3)**.  
  - **Feature engineering**: continent from nationality; `CheckInRatio`; `TimeBetweenVisits`; equal‑frequency **binning** for key numerics; one‑hot encoding; **Min‑Max scaling**.  
  - **Selection & PCA** to retain signal while reducing dimensionality.
- **Modeling** — K‑Means with multiple initializations; *k* chosen via **elbow**; **silhouette** assessed; clusters interpreted with domain knowledge.
- **Evaluation** — Balance (size vs. magnitude), separation, and qualitative profile interpretability; mapped to business goals.
- **Deployment & Maintenance** — Documentation, staff enablement, monitoring, and **periodic retraining** with fresh data.

---

## 4) Segmentation Output (Profiles)

The analysis yields **six segments**: five post‑hoc K‑Means clusters plus one *a priori* cluster for “services‑only” customers. Brief characterizations:

- **Cluster 0 — Youth Adventurers**: younger, mixed geographies, agency‑booked, moderate lodging / low ancillary spend.  
- **Cluster 1 — Diverse High Spenders**: all ages, highest spend (lodging & ancillary), higher share of **direct** bookings; preferences for quiet, king bed / crib, high floors.  
- **Cluster 2 — Senior Globetrotters**: older, Europe + Americas, average spend, more agency‑booked; prefer rooms away from elevator.  
- **Cluster 3 — Mature Leisure Enthusiasts**: older, higher **hotel‑facilities** spend, plan further ahead; prefer twin beds.  
- **Cluster 4 — Midlife Explorers**: 40–55, more **corporate/GDS** channel usage, average spend.  
- **Cluster 5 — Last‑Minute Visitors (*a priori*)**: all ages, low revenue, agency/operator, book on arrival.

Each profile is paired with marketing **plays** (positioning, channels, offers, seasonality, partnerships). 

---

## 5) Getting Started

### Prerequisites
- Python ≥ 3.10
- `pip` or `uv`/`poetry`
- (Optional) `make`

### Installation
```bash
# clone your fork
git clone https://github.com/<your-org>/<your-repo>.git
cd <your-repo>

# create & activate a virtual environment (example with venv)
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# install dependencies
pip install -r requirements.txt
```

### Environment
Create a `.env` (or `config.yaml`) to point to your data locations:
```ini
DATA_DIR=./data
RAW_DIR=${DATA_DIR}/raw
PROCESSED_DIR=${DATA_DIR}/processed
SEED=42
```

> **Data privacy**: Do **not** commit raw data or PII. Use anonymized hashes only (as in the report).

---


**Option B — Pipeline (CLI)**
```bash
python -m src.pipelines.run   --raw ./data/raw/customers.csv   --out ./data/processed   --seed 42
```

Outputs include cluster assignments, centroids, diagnostics, and profile cards.

---

## 7) Models & Features (Key Choices)

- **Imputation**: KNN (k=3) for `Age`; domain‑based thresholds for outliers.  
- **Engineering**: `Continent`, `TimeBetweenVisits`, `CheckInRatio`; equal‑frequency binning for `Age`, `AverageLeadTime`, `LodgingRevenue`, `OtherRevenue`.  
- **Selection**: domain subset + **PCA** for compactness.  
- **Clustering**: **K‑Means** (multiple `n_init`), *k* via **Elbow**; **silhouette** for separation.  
- **Validation**: quantitative indices + qualitative marketing interpretability.

> See in‑code docstrings and the notebooks for exact parameters and justifications.

---

## 8) Business Application (Examples)

- **Cluster 1 (High Spenders)** — emphasize direct‑booking benefits, family‑friendly upgrades, and personalized offers; loyalty triggers (birthdays, room upgrades).  
- **Cluster 0 (Youth)** — affordable, experiential bundles; social media/influencer partnerships; off‑season promotions.  
- **Cluster 2/3 (Senior & Mature Leisure)** — cultural/wellness packages, early‑booking incentives, partnerships with local businesses; channels like Facebook and travel magazines.  
- **Cluster 4 (Midlife/Corporate)** — B2B/GDS optimization, corporate retreats, LinkedIn/email nurtures.  
- **Cluster 5 (Last‑Minute)** — dynamic/last‑minute deals and short‑window bundles across agency channels.

---

## 9) Limitations & Next Steps

- Add **length‑of‑stay**, **family size**, **stay purpose** (business/leisure), and **satisfaction** to refine segments.  
- Introduce **psychographic** factors (motivations, perceived experience).  
- Track **model drift**; schedule periodic retraining and re‑profiling.  
- Explore alternative clustering (Gaussian Mixtures, HDBSCAN) and **model selection** criteria beyond Elbow/Silhouette.

---

## 10) References
 
- Woungang, I., et al. (2023). *Advanced Network Technologies and Intelligent Computing*. Springer.  
- Mauri, A. G. (2012). *Hotel Revenue Management: Principles and Practices*. Pearson Italia.  
- Guillet, B., Guo, Y., & Law, R. (2015). Segmenting hotel customers… *Journal of Travel and Tourism Marketing*.Segmenting hotel customers based on rate fences through
conjoint and cluster analysis. Journal of Travel and Tourism Marketing, p. 835-851. doi:
10.1080/10548408.2015.1063825 
- Rondan-Cataluña, F. J. & Rosa-Diaz, I. M. (2014). Segmenting hotel clients by pricing variables and value
for money. Current Issues in Tourism. Vol. 17, no. 1, p. 60–71. doi: 10.1080/13683500.2012.718322.
- Chalupa, S., Chromy, J. & Cech, P. (2019). Behavioral Segmentation of Hotel Customers: An Empirical
Study. Education Excellence and Innovation Management Through Vision 2020, p. 2113-2119.


## 12) Acknowledgments

NOVA IMS — MDSAA, Faculty and colleagues, Group R contributors.

