# Janus

**Challenge:** Dyania Health - early detection of liver disease risk in patients with compounding cardiometabolic-kidney conditions.  
**Event:** LifeHack Athens Hackathon, May 27-28, 2026.  
**Team:** Janus.  
**Proposed System:** CKM-MASH Sentinel.  
**Core Deliverable:** MASH/fibrosis risk stratification protocol, ML methodology, data plan, notebook proof of concept, and presentation materials.

## Team Members

| Member | Role / Affiliation |
|---|---|
| Maria Dima | Medicine Student, NKUA |
| Iosif Chatzimichalis | Medicine Student, NKUA |
| Ioanna Sofia Theofylatou | Biochemist/Biotechnologist, UTh |
| Giorgos Mamounas | Pharmacist, AUTH |
| Despoina Founta | Biotechnologist, AUA |

---

## Problem Statement

Metabolic dysfunction-associated steatohepatitis (MASH) is frequently underdiagnosed in patients with cardiometabolic-kidney (CKM) risk factors because early disease is often clinically silent or attributed to isolated metabolic conditions. This creates a major detection gap across primary care, endocrinology, cardiology, and nephrology workflows.

Late identification increases the risk of advanced fibrosis, cirrhosis, cardiovascular events, CKD progression, hospitalization, mortality, and missed opportunities for early intervention, hepatology referral, non-invasive fibrosis assessment, and clinical-trial recruitment.

---

## Our Approach

Our team proposes an EHR-integrated machine learning pre-screening system that passively identifies adults across the CKM spectrum who may have undetected clinically significant MASH/fibrosis. The system uses routinely available pre-index clinical data, including demographics, BMI, blood pressure, HbA1c, lipid markers, AST/ALT, platelet count, eGFR, albuminuria where available, diagnosis codes, medication exposures, smoking history, and derived scores such as FIB-4.

Multiple supervised models, including XGBoost, ElasticNet logistic regression, PLS, LightGBM, and CatBoost, are considered for discrimination, calibration, interpretability, and clinical deployability. In the implemented NHANES proof of concept, XGBoost, ElasticNet, LightGBM, CatBoost, sample-weighted XGBoost, and calibrated FIB-4/APRI/NFS baselines were evaluated.

The final system generates a calibrated CKM-MASH risk score, classifies patients into Low, Intermediate, or High action tiers, and recommends next-step actions such as FibroScan, hepatology referral, intensified metabolic management, nephrology/cardiology follow-up where clinically appropriate, or clinical trial eligibility review.

---

## Key Design Decisions

| Decision | Reasoning |
|---|---|
| Define the clinical study population first | The workflow begins with clear clinical variables, target population, and inclusion/exclusion criteria so the system focuses on adults at elevated CKM risk who are most likely to benefit from early MASH screening. |
| Use continuous EHR-integrated deployment | The platform is designed to run passively across primary care, metabolic, cardiology, nephrology, and hepatology settings using existing patient data without requiring extra manual clinician input. |
| Use structured pre-index clinical data as model inputs | Predictors are limited to data available before the prediction date, including demographics, labs, vitals, diagnoses, medications, smoking history, and derived scores. This reduces data leakage and reflects real-world prospective deployment. |
| Benchmark multiple supervised ML models | XGBoost, ElasticNet logistic regression, PLS, LightGBM, and CatBoost are evaluated or proposed to balance predictive performance, calibration, interpretability, and clinical deployability. |
| Use clinically meaningful derived features | FIB-4, APRI, NAFLD Fibrosis Score, AST/ALT ratio, CKD indicators, metabolic syndrome proxy, medication-supported phenotypes, and CKM burden count translate raw EHR data into medically interpretable predictors. |
| Define proof-of-concept ground truth using elevated liver stiffness | Due to hackathon constraints, the proof of concept predicts LSM >=8.0 kPa using retrospective elastography labels as a marker of clinically significant fibrosis risk. A stricter LSM >=12.0 kPa endpoint is used as sensitivity analysis. |
| Include calibration and Decision Curve Analysis | The model is evaluated not only for classification accuracy but also for probability reliability and clinical decision-making value, which are essential for safe triage use. |
| Provide actionable and interpretable outputs | The product returns a continuous risk score, Low/Intermediate/High risk tier, recommended next-step actions, grouped feature contributions, and SHAP explanations for audit and clinical review. |
| Avoid label leakage | FibroScan kPa, CAP, biopsy, imaging-confirmed fibrosis, explicit MASH diagnosis text, and fibrosis-stage text are labels or adjudication evidence, not predictors. |
| Keep the protocol ideal and document prototype deviations | The study protocol describes a hospital-ready design. The NHANES implementation is a reproducible proof of concept with explicit limitations around cross-sectional timing, available variables, and proxy labels. |

---

## Clinician-Facing Output

```text
MASH/fibrosis risk score: 72/100
Risk tier: High
Recommended next step: FibroScan / hepatology referral

Main contributors:
+ FIB-4 / AST-ALT-platelet pattern: +18
+ Type 2 diabetes + obesity: +14
+ Triglycerides / HDL pattern: +8
+ No prior liver staging despite abnormal ALT: +6
- Preserved albumin and eGFR: -5
```

Default product view: simplified clinician explanation panel.  
Audit view: full SHAP waterfall plot and model governance details.

---

## Proof-of-Concept Summary

The implemented prototype uses NHANES 2017-March 2020 public-use data because it contains routine cardiometabolic/liver variables plus liver ultrasound transient elastography and CAP. The primary cohort is CKM-enriched adults with diabetes or at least two CKM markers. A broader at-least-one-CKM cohort is retained for sensitivity analysis. Synthetic clinical notes from the Dyania package are used only for abstraction/audit demonstration because they contain label-bearing diagnosis, FibroScan, biopsy, and fibrosis-stage text.

### Cohort Flow

| Step | N |
|---|---:|
| Total NHANES rows loaded | 15,560 |
| Adults aged >=18 | 9,693 |
| Adults with complete elastography | 7,768 |
| CKM-primary labeled before alcohol exclusion | 4,827 |
| High-alcohol exclusions | 182 |
| Final CKM-primary labeled cohort | 4,645 |
| LSM >=8.0 kPa positives | 646 |
| LSM >=12.0 kPa positives | 222 |

### Model Performance

The pre-specified validation rule selected a 60/20/20 train/validation/test split. Hyperparameters, calibration, and thresholds were selected without using the held-out test labels.

| Metric | Calibrated XGBoost |
|---|---:|
| AUROC | 0.804 |
| AUPRC | 0.444 |
| Brier score | 0.098 |
| Bootstrap AUROC 95% CI | 0.766-0.842 |
| High-risk threshold | 24.4% |
| High-risk sensitivity | 66.7% |
| High-risk specificity | 78.4% |
| High-risk PPV | 33.2% |
| High-risk NPV | 93.6% |
| Escalate-above-low sensitivity | 94.6% |
| Escalate-above-low NPV | 97.8% |

The highest predicted-risk quartile had 34.5% observed LSM >=8.0 kPa compared with 1.7% in the lowest quartile, showing a clinically meaningful risk gradient.

---

## Repository Structure

```text
.
|-- README.md
|-- protocol/
|   `-- study_protocol.md
|-- ml/
|   |-- approach.md
|   |-- nhanes_mash_pipeline.py
|   |-- generate_reporting_artifacts.py
|   `-- write_sensitivity_artifacts.py
|-- data/
|   |-- data_plan.md
|   |-- raw/nhanes/
|   `-- processed/nhanes_mash_model.csv
|-- notebooks/
|   |-- dyania_nhanes_mash_risk_stratification_full_code.ipynb
|   |-- dyania_nhanes_mash_risk_stratification.ipynb
|   `-- README.md
|-- artifacts/
|   |-- cohort_summary.json
|   |-- metrics.json
|   |-- model_comparison.md
|   |-- risk_tier_metrics.md
|   |-- sensitivity_analysis_same_model.md
|   |-- risk_quartile_incidence.png
|   |-- shap_xgboost_global_importance.png
|   |-- note_1_clinician_detailed_card.png
|   `-- note_2_clinician_detailed_card.png
|-- presentation/
|   |-- slides.md
|   |-- slides.pdf
|   `-- dyania_presentation_ml_slide_added.pptx
`-- tests/
    `-- test_nhanes_pipeline.py
```

---

## Required Deliverables

| Required File | Status |
|---|---|
| `README.md` | Project overview, problem statement, approach, design decisions, proof-of-concept results, and repository guide. |
| `protocol/study_protocol.md` | Full study design including objectives, endpoints, cohort definition, statistical plan, clinical integration, ethics, and privacy. |
| `ml/approach.md` | ML methodology, chosen models, feature engineering, calibration, validation strategy, SHAP explanations, and prototype deviations. |
| `data/data_plan.md` | Data sources, preprocessing, harmonization, missingness, label construction, leakage controls, and governance assumptions. |
| `presentation/slides.pdf` / `.pptx` | Presentation materials summarizing the proposal and ML proof of concept. |
| `notebooks/` | Full-code notebook and shorter narrative notebook for proof-of-concept review. |

---

## How to Run

Recommended notebook submission:

```text
notebooks/dyania_nhanes_mash_risk_stratification_full_code.ipynb
```

The notebook uses precomputed artifacts by default for fast review. Set `RUN_FULL_PIPELINE = True` inside the notebook to recompute the NHANES pipeline from source files.

Script-based run:

```powershell
& 'C:\Users\Sifis\AppData\Local\Programs\Python\Python313\python.exe' ml\nhanes_mash_pipeline.py --bootstrap 300
& 'C:\Users\Sifis\AppData\Local\Programs\Python\Python313\python.exe' ml\generate_reporting_artifacts.py
& 'C:\Users\Sifis\AppData\Local\Programs\Python\Python313\python.exe' ml\write_sensitivity_artifacts.py
```

Run tests:

```powershell
& 'C:\Users\Sifis\AppData\Local\Programs\Python\Python313\python.exe' -m unittest discover -s tests -v
```

---

## Key Artifacts

| Artifact | Purpose |
|---|---|
| `artifacts/model_comparison.md` | Model comparison across FIB-4, APRI, NFS, ElasticNet, XGBoost, LightGBM, CatBoost, and weighted XGBoost sensitivity. |
| `artifacts/risk_tier_metrics.md` | Low / Intermediate / High risk-tier outcomes and action-threshold metrics. |
| `artifacts/sensitivity_analysis_same_model.md` | Same-model sensitivity analyses on the locked held-out predictions. |
| `artifacts/risk_quartile_incidence.md` | Observed endpoint incidence by predicted-risk quartile. |
| `artifacts/elasticnet_standardized_odds_ratios.md` | Transparent ElasticNet odds-ratio benchmark. |
| `artifacts/shap_xgboost_global_importance.png` | Global XGBoost feature-importance visualization. |
| `artifacts/note_1_clinician_detailed_card.png` | Clinician-oriented risk-card demo for Dyania Note 1. |
| `artifacts/note_2_clinician_detailed_card.png` | Clinician-oriented risk-card demo for Dyania Note 2. |

---

## Validation and Limitations

This is a proof of concept, not a clinically deployed model. NHANES is cross-sectional, and LSM >=8.0 kPa is a fibrosis-risk proxy rather than biopsy-confirmed MASH. External validation is proposed in UK Biobank imaging/EHR, followed by temporal hospital EHR validation and prospective monitoring.

Before deployment, the model would require:

- Site-specific calibration.
- External and temporal validation.
- Subgroup fairness review.
- Clinician review of false negatives and intermediate-risk routing.
- Clear governance for note abstraction, PHI handling, and model monitoring.

---

## References

- Dyania Health hackathon case study: https://github.com/dyaniahealth/dyania-hackathon-case-study
- NHANES 2017-March 2020 liver ultrasound transient elastography: https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_LUX.htm
- MASHRisk, npj Digital Medicine 2026: https://www.nature.com/articles/s41746-025-02220-x
- AASLD practice guidance for NAFLD/MASLD risk stratification: https://pubmed.ncbi.nlm.nih.gov/36727674/
- Tobacco and NAFLD systematic review/meta-analysis: https://pmc.ncbi.nlm.nih.gov/articles/PMC12568600/
- SHAP waterfall plots: https://shap.readthedocs.io/en/latest/generated/shap.plots.waterfall.html
