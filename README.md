# FMCG Retail Analytics Portfolio

**Panos Emmanouilidis** · Data Scientist & Analytics Consultant  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat&logo=linkedin)](https://www.linkedin.com/in/panosemmanouilidis)

---

## Featured Project: Promotional Analytics — Sell-Out Forecasting & Trade ROI Optimisation

### The Business Problem

A global FMCG manufacturer running thousands of trade promotions annually across Europe and Asia had no reliable way to forecast promotional sell-out volume or evaluate return on investment before committing trade spend. Planning was driven by intuition and historical precedent.

The consequences were measurable:

- Planners in **Market A (Europe)** were systematically **over-forecasting by 35%** — excess inventory and overstated investment cases
- Planners in **Market B (Asia)** were **under-forecasting by 103%** — significant demand unmet, growth opportunity unrealised
- **60–79% of promotions** across both markets missed their volume plan by more than 50% with no way to identify this risk in advance
- No pre-execution ROI visibility — no mechanism to know which promotions would generate positive incremental gross profit before committing spend

---

### What Was Built

- **XGBoost sell-out forecasting model** — R² 0.81 (Market A), R² 0.70 (Market B), 5x improvement over linear baseline
- **Pre-execution ROI framework** — volume forecast and ROI estimate before any promotion runs
- **Full GCP Vertex AI deployment** — live prediction endpoints serving real-time forecasts via REST API
- **Streamlit planner application on Cloud Run** — business-facing interface requiring no technical skills
- **SHAP explainability layer** — identifies why a promotion is predicted to succeed or fail, in plain language
- **Data quality resolution** — cleaned and validated 700K+ promotional records across two markets

![Deployment Architecture](promotional-analytics-architecture.svg)

---

### Key Business Outcomes

---

### Key Business Outcomes

| Metric | Market A | Market B |
|--------|----------|----------|
| Model accuracy (R²) | **0.81** | **0.70** |
| Improvement over baseline | 5x | 5x |
| Planning bias identified | -35% over-forecast | +103% under-forecast |
| Promos missing plan by >50% | 60% | 79% |
| Best performing mechanic ROI | +0.09 | +10.12 |
| Worst performing mechanic ROI | -0.11 | -10.00 |

---

### 🔗 Try It & Explore the Code

| | Link |
|---|---|
| 🚀 **Live Forecaster App** | [Open Streamlit App](https://unilever-promo-forecaster-128825737789.europe-west2.run.app) |
| 💻 **Full Technical Repository** | [fmcg-ds-technical-portfolio](https://github.com/Panosemmanouilidis2/fmcg-ds-technical-portfolio) |

---

### 📓 Run the Notebooks — No Setup Needed

Click any badge to open directly in Google Colab and run in your browser:

| Notebook | Description | Open |
|---|---|---|
| 01 — EDA & Forecast Accuracy | Data exploration, planning bias, market comparison | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Panosemmanouilidis2/fmcg-ds-technical-portfolio/blob/main/promotional-analytics/notebooks/01_eda_planning_accuracy.ipynb) |
| 02 — Feature Engineering & Training | 6 model families, XGBoost tuning, overfitting check | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Panosemmanouilidis2/fmcg-ds-technical-portfolio/blob/main/promotional-analytics/notebooks/02_feature_engineering.ipynb) |
| 03 — Vertex AI Deployment | GCS, model registry, endpoint deployment *(GCP account required)* | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Panosemmanouilidis2/fmcg-ds-technical-portfolio/blob/main/promotional-analytics/notebooks/03_model_training.ipynb) |
| 04 — Evaluation & SHAP | Metrics, residuals, feature importance, ROI classification | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Panosemmanouilidis2/fmcg-ds-technical-portfolio/blob/main/promotional-analytics/notebooks/04_model_evaluation_shap.ipynb) |

---

### Tech Stack

| Layer | Technology |
|-------|-----------|
| Language | Python 3.10 |
| ML Modelling | XGBoost, LightGBM, Scikit-learn |
| Explainability | SHAP |
| Data Engineering | Pandas, NumPy |
| Cloud Platform | Google Cloud Platform (GCP) |
| Model Deployment | Vertex AI Model Registry, REST endpoints |
| Containerisation | Docker |
| Application | Streamlit on Cloud Run |

---

### Data & Confidentiality Notice

Developed against live commercial sell-out and sell-in data for a global FMCG manufacturer. Client identity, retail customer names, and raw datasets are not disclosed in line with professional confidentiality obligations. All data in this repository is synthetically generated to preserve the statistical properties of the original. Planning accuracy biases, ROI distributions, and mechanic performance rankings reflect real analytical findings. Methodology, deployment architecture, and business outcomes are genuine.
All portfolio materials have been independently developed and do not contain proprietary data, trade secrets, or confidential business information belonging to any third party.

---

### About

10+ years of FMCG and retail analytics experience spanning manufacturer and retailer perspectives. Specialising in demand forecasting, promotional analytics, on-shelf availability, and end-to-end ML deployment.

📧 [Get in touch via LinkedIn](https://www.linkedin.com/in/panosemmanouilidis)

---

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)

*Licensed under Creative Commons Attribution-NonCommercial 4.0. You may share and adapt for non-commercial purposes with attribution. Commercial use not permitted.*
