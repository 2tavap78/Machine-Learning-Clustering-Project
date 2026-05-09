# Reach for Change — Donor Profiling Through Clustering

Exploratory segmentation study using K-Means clustering to uncover distinct donor profiles within a nonprofit supporter database. The goal is to help the **Civic Support Alliance (CSA)** move away from generic mass outreach and toward personalized, data-driven fundraising strategies.

---

## Project Overview

Nonprofit organizations increasingly face donor fatigue caused by untargeted solicitations. This project applies unsupervised learning to segment donors based on their behavioral, financial, and neighborhood-level characteristics — providing the foundation for more respectful and effective campaign planning.

---

## Dataset

The dataset (`donors_descriptive.csv`) contains ~95 attributes per donor, covering:

- **Sociodemographics** — age, gender, home ownership, urbanicity, SES, income group, wealth rating, number of children
- **Neighborhood-level indicators** — median home value, median household income, per capita income, owner-occupied housing rate, military/veteran population percentages
- **Campaign interaction & donation behavior** — lifetime and recent promotion history, response rates, recency/frequency status, months since first/last gift
- **Donation amounts** — lifetime totals, recent averages, card-specific donations, min/max gift amounts

---

## Methodology

### 1. Preprocessing & EDA
- Data categorized into four thematic sub-dataframes for targeted analysis
- Missing values handled via KNN imputation
- Negative values (implausible for percentage/monetary fields) flagged and treated as invalid
- Skewness assessed across all numeric features; Yeo-Johnson power transformation applied to right-skewed variables
- Outliers capped at the 99th percentile prior to transformation
- Min-Max scaling applied after transformation
- Engineered features: `AVG_DONATION`, `LIFETIME_GIFT_PER_PROM`, `DONOR_STATUS`, `MONTHS_SINCE_FIRST_GIFT_BIN`

### 2. Dimensionality Reduction
- PCA applied to the financial neighborhood indicators (Group A), retaining components that explain ≥ 85% of variance
- PCA intentionally skipped for the military affiliation group (Group B) due to the interpretive value of keeping individual features distinct

### 3. Clustering Perspectives

Two clustering perspectives were implemented:

**Perspective 1 — Neighborhood-Level Indicators**  
Features: financial PCA components + military/veteran affiliation percentages  
Segments donors by the socioeconomic and community context of where they live.

**Perspective 2 — Donation Response & Amounts** *(primary perspective)*  
Features: lifetime gift amount, max/min/average gift, recent average card gift, lifetime gift count, response proportions, frequency status  
Segments donors by *how much* they give and *how engaged* they are with campaigns.

K-Means was used for both perspectives. The optimal number of clusters was selected via the elbow method.

---

## Clustering Results (Perspective 2 — 4 Clusters)

| Cluster | Label | Profile | Key Metrics |
|---------|-------|---------|-------------|
| **0** | Responsive but small donors | High engagement, moderate frequency (~3 donations/year), below-average amounts | High `FREQUENCY_STATUS`, second-highest `LIFETIME_GIFT_COUNT` |
| **1** | High-value lapsed donors | Highest average donation, lowest donation count, relatively recent first gift | Highest `RECENT_AVG_GIFT_AMT`, lowest `LIFETIME_GIFT_COUNT` |
| **2** | Disengaged average donors | Largest segment (~40%), low response rates, likely one-time donors | Low `RECENT_STAR_STATUS`, long tenure, low `LIFETIME_GIFT_COUNT` |
| **3** | Best overall donors | Highest frequency and amounts, highly active, long-standing relationship | Highest across all donation and engagement metrics |

**Cross-cluster notes:**
- Donor age is consistent across clusters (~50–60 years) — all communication should maintain a respectful, adult tone
- Income group is similar across clusters (groups 3–4) — income alone does not predict donation size

---

## Action Plan

| Cluster | Recommended Approach |
|---------|----------------------|
| **Cluster 3** — Best donors | Highly personalized outreach: exclusive newsletters, recognition programs, occasional direct contact |
| **Cluster 1** — High-value lapsed | Personalized retention campaigns focused on building long-term relationship and demonstrating impact |
| **Cluster 0** — Engaged small donors | Automated email campaigns and website prompts; upsell messaging to increase donation size |
| **Cluster 2** — Disengaged majority | Low-cost bulk email reactivation campaigns; maximize reach at minimal cost |

---

## Tech Stack

- **Python** — pandas, NumPy, scikit-learn, SciPy, Matplotlib, Seaborn
- **Algorithms** — K-Means, PCA, KNN Imputation
- **Transforms** — Yeo-Johnson (PowerTransformer), Min-Max Scaling, StandardScaler
- **Environment** — Google Colab / Jupyter Notebook

---

## Repository Structure

```
├── Donors_Analysis_Final.ipynb   # Main analysis notebook
├── donors_descriptive.csv              # Input dataset (not included)
└── README.md
```

---

## Key Findings

- Donor income and age are not reliable differentiators between segments — behavioral and donation history variables carry far more predictive signal
- Campaign exposure was uniform across all clusters (no personalization in historical campaigns), confirming the need for targeted segmentation going forward
- A 4-cluster solution outperformed k=5, where two clusters were near-identical and collapsed into a single meaningful group upon reduction
