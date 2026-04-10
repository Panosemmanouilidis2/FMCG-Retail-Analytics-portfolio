# FMCG Promotional Analytics | GCP + Vertex AI

> End-to-end ML pipeline predicting promotional sell-out volume and 
> optimising trade ROI across UK and Indonesia markets. Built on GCP 
> with Vertex AI deployment and a live Streamlit forecasting application.

---

## Business Problem

FMCG manufacturers run thousands of trade promotions annually but lack 
a data-driven method to forecast sell-out volume and ROI before committing 
trade spend. This project replaces manual gut-feel planning with a 
machine learning pipeline that answers three core questions before a 
promotion runs:

- How many units will this promotion sell?
- What ROI will it generate per pound of trade spend?
- Is the promotion scheduled in the right week for the category?

---

## Business Value

- Identifies a **9.5 percentage-point gap** between promotions planners 
  believe are profitable and those the model predicts will be — 
  representing material trade spend misallocation at scale
- Quantifies that **60%+ of UK promotions miss volume targets by >50%** 
  — systemic bias, not random noise
- Proves that **70% of UK sell-out variance is driven by seasonality**, 
  not promotion mechanics — fundamentally changing how trade teams 
  should plan and review performance
- Separates **commercial failures from exogenous failures** (on-shelf 
  availability and weather) — enabling targeted supply chain 
  interventions rather than commercial ones
- Provides planners with **pre-execution ROI estimates** before 
  committing budget, via a live prediction API
- A single avoided underperforming promotion (typically **£50K–£500K** 
  in wasted trade spend) pays for years of infrastructure

---

## Markets & Dataset Scale

| Dimension | UK | Indonesia |
|---|---|---|
| Clean rows | 233,218 | 404,256 |
| Raw data size | 146 MB | 119 MB |
| Features used | 97 | 78 |
| Customers / channels | 7 major grocers | 35 retail channels |
| Promotion mechanics | 8 types | 11 types |
| Target variable | ActualPromoSalesVolumeSellOut | ActualNetPromoSalesVolumeSellOut |
| **Total** | **637,474 rows** | **265 MB** |

> Indonesia has 73% more rows than UK but a smaller file size, 
> reflecting fewer columns (78 vs 97) and more compact data structure.

---

## Approach

### Pipeline Architecture
```
Raw data → Cleaning → EDA & Planning Accuracy → Feature Engineering 
→ Categorical Encoding → Chronological Train/Test Split 
→ 6-Model Evaluation → Hyperparameter Tuning → SHAP Explainability 
→ iGP/ROI Derivation → Vertex AI Deployment → Streamlit App
```

### Key Methodology Decisions
- **Target transformation:** log1p applied to heavily right-skewed 
  sell-out volumes; reversed with expm1 at evaluation
- **Train/test split:** chronological (shuffle=False) — the model must 
  forecast future promotions based only on historical patterns
- **Data leakage prevention:** all Actual columns excluded from 
  features; scaler fit on train only
- **Hyperparameter tuning:** two-stage approach (RandomizedSearchCV on 
  50K sample, retrain on full dataset) — completes in 5 minutes vs 
  2–3 hours for full grid search
- **Indonesia-specific:** chronological last-50K rows used for tuning 
  sample (not random) due to strong time trend in the data

---

## Model Results

### UK — XGBoost (tuned) — Champion

| Model | RMSE | MAE | R² |
|---|---|---|---|
| Linear Regression (baseline) | 3.07 | 2.47 | 0.15 |
| Random Forest | 2.74 | 2.15 | 0.32 |
| XGBoost (default) | 2.12 | 1.63 | 0.59 |
| LightGBM | 2.16 | 1.66 | 0.58 |
| **XGBoost (tuned)** | **1.46** | **0.98** | **0.81** |

**5x improvement over linear baseline. Train R²: 0.92 / Test R²: 0.81 
(gap: 0.11 — acceptable for noisy retail data)**

### Indonesia — XGBoost (conservative tuned) — Champion

| Model | RMSE | MAE | R² |
|---|---|---|---|
| Linear models (baseline) | 2.31–2.32 | 1.67–1.69 | 0.27–0.28 |
| Random Forest | 1.61 | 1.25 | 0.65 |
| XGBoost (default) | 1.48 | 1.15 | 0.70 |
| **XGBoost (conservative tuned)** | **1.49** | **1.15** | **0.70** |

**2.5x improvement over linear baseline. Conservative tuning applied 
to control overfitting gap (train R²: 0.85 / test R²: 0.70)**

> All metrics on log1p-transformed target. Reversed with expm1 
> for volume interpretation.

---

## Key Findings

1. **Seasonality dominates UK sell-out** — time features explain 69.9% 
   of model predictions vs 11.8% for promo mechanics. A mediocre 
   promotion in week 48 (pre-Christmas) consistently outsells an 
   excellent promotion in week 8 (February)
2. **UK planners systematically over-forecast by 35%** — median 
   planning accuracy ratio: 0.647
3. **Indonesia planners systematically under-forecast by 103%** — 
   median planning accuracy ratio: 2.025. Opposite directional bias 
   requiring market-specific recalibration
4. **60%+ of promos miss targets by >50%** in both markets — this is 
   structural, not random
5. **Failed promo cluster is driven by OSA and weather** — the 
   bottom-left scatter cluster (high predicted, near-zero actual) 
   represents availability failures and adverse weather events, not 
   commercial failures
6. **UK Loyalty mechanics deliver the strongest ROI** — median +0.09 
   vs Shopper Marketing at -0.11
7. **Indonesia Discount RP mechanic destroys value consistently** — 
   only 13% of promotions using this mechanic are profitable

---

## Deployment

| Component | Detail |
|---|---|
| UK endpoint | Vertex AI — europe-west2 |
| Indonesia endpoint | Vertex AI — europe-west2 |
| Forecasting app | Streamlit — deployed to GCP Cloud Run |
| Model storage | GCS bucket |
| Containers | Docker — Artifact Registry |
| Monitoring | GCP Cloud Monitoring |

---

## Infrastructure Cost

### Assumptions
- GCP region: europe-west2
- Vertex AI node type: n1-standard-2 (~£0.033/hr per endpoint)
- 2 endpoints live simultaneously (UK + Indonesia)
- **Business hours:** 8hrs/day × 20 working days = 160 hrs/month
- **Demo/portfolio:** 1 prediction run/day × ~30 min session = 
  ~15 hrs/month
- **Always on:** 720 hrs/month (24/7, no downtime)
- **Current actual:** ~60 hrs/month (based on observed £8.97 bill; 
  consistent with ad-hoc development usage including idle Workbench VM)
- One-off CapEx: model training at ~2 hrs compute per market
- Cost per prediction: based on ~500ms average endpoint response time

### Monthly Cost by Scenario

| Scenario | Monthly £ | Annual £ |
|---|---|---|
| Always on (24/7) | £47.20 | £566 |
| Business hours (8h × 20 days) | £10.90 | £131 |
| Demo / portfolio (1 run/day) | £1.80 | £22 |
| **Current actual** | **£8.97** | **£108** |
| One-off setup (CapEx, per market) | £0.77 | — |

### Cost Efficiency by Market

| Market | Rows | CapEx per 1K rows | Monthly OpEx per 1K rows (business hrs) |
|---|---|---|---|
| UK | 233,218 | £0.003 | £0.047 |
| Indonesia | 404,256 | £0.002 | £0.027 |
| **Combined** | **637,474** | **£0.001** | **£0.017** |

> Indonesia cost per row is lower because the fixed endpoint cost 
> spreads across a larger dataset — larger datasets do not increase 
> costs proportionally.

### Cost Per Prediction

| Volume | Cost |
|---|---|
| 1 prediction | £0.002 |
| 100 predictions/month | £0.18 |
| 1,000 predictions/month | £1.80 |

### Scalability
Each additional market costs ~£0.77 to deploy (one-off) and 
~£5.45/month to operate at business hours — the pipeline is 
market-agnostic by design.

### Context
> The entire annual infrastructure cost at business hours (£131) is 
> recovered by avoiding a single underperforming promotion. A typical 
> wasted trade promotion costs £50,000–£500,000 in misallocated spend.

---

## Stack

| Layer | Technologies |
|---|---|
| Modelling | XGBoost, LightGBM, scikit-learn, SHAP |
| Data processing | Python, pandas, numpy |
| Cloud infrastructure | GCP Vertex AI, Cloud Run, GCS, Artifact Registry |
| Application | Streamlit |
| Containers | Docker |

---

## Repository Structure

```
fmcg-promotional-analytics/
│
├── README.md
│
├── notebooks/
│   ├── 01_data_cleaning_uk.ipynb
│   ├── 02_data_cleaning_indonesia.ipynb
│   ├── 03_eda_and_planning_accuracy.ipynb
│   ├── 04_feature_engineering.ipynb
│   ├── 05_model_training_uk.ipynb
│   ├── 06_model_training_indonesia.ipynb
│   ├── 07_shap_explainability.ipynb
│   └── 08_roi_derivation.ipynb
│
├── src/
│   ├── serve_uk.py              # Vertex AI serving script (UK)
│   ├── serve_indonesia.py       # Vertex AI serving script (Indonesia)
│   ├── predict.py               # Feature engineering for inference
│   └── save_encoders.py         # Median & scaling group computation
│
├── app/
│   ├── app.py                   # Streamlit forecasting application
│   ├── Dockerfile               # Cloud Run container
│   └── requirements.txt
│
├── deployment/
│   ├── Dockerfile.uk            # Vertex AI serving container (UK)
│   ├── Dockerfile.indonesia     # Vertex AI serving container (Indonesia)
│   └── deploy.py                # Vertex AI deployment script
│
├── models/
│   └── .gitkeep                 # Placeholder — model files not tracked
│
├── outputs/
│   ├── shap_bar.png
│   ├── shap_beeswarm.png
│   └── xgboost_evaluation.png
│
├── .gitignore
└── requirements.txt
```

### .gitignore
```
*.csv
*.pkl
*.json
*.env
models/*.pkl
__pycache__/
.ipynb_checkpoints/
```

---

## Notes

Built as an FMCG analytics consulting portfolio piece demonstrating 
end-to-end ML delivery from raw data to live production API. Client 
data not included — pipeline structure, methodology, and all 
deployment scripts are fully documented.

*Author: Panos Emmanouilidis — Data Analytics Consultant*
