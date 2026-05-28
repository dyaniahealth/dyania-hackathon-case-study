# Data Plan

## 1. Data Role in the Submission

The data strategy supports a MASH-first risk stratification model for CKM-enriched adults. The core principle is simple:

> Predict risk before confirmatory liver staging; use FibroScan, biopsy, imaging, and explicit MASH text only as labels or adjudication evidence.

This prevents label leakage and keeps the proposed workflow clinically useful. If a patient already has a FibroScan result, biopsy stage, or explicit MASH diagnosis in the prediction window, the problem is no longer early risk stratification; it becomes chart abstraction or case confirmation.

---

## 2. Data Sources for Real Deployment

| Source | Data Type | Access Pathway | Variables Used | Role |
|---|---|---|---|---|
| EHR structured labs | Longitudinal lab results | FHIR, OMOP, or hospital data warehouse | AST, ALT, platelets, albumin, bilirubin, alkaline phosphatase, HbA1c, glucose, lipids, creatinine, eGFR, urine albumin/creatinine | Predictors and engineered scores |
| EHR diagnoses and encounters | Structured clinical history | ICD-10, SNOMED, problem lists, encounter tables | Diabetes, obesity, hypertension, dyslipidemia, CKD, CVD, liver disease exclusions, hepatology referral | CKM enrichment, exclusions, care gaps |
| Vitals and anthropometrics | Structured exam data | Vitals table, flowsheets | BMI, weight, blood pressure, waist circumference if available | Predictors |
| Medication records | Prescriptions/administrations | Medication orders, pharmacy claims | GLP-1 receptor agonists, SGLT2 inhibitors, metformin, insulin, statins, antihypertensives, lipid therapy | Diagnosis cross-checking and risk context |
| Social history | Structured EHR fields or validated abstraction | Social history table, flowsheets, note abstraction | Never/former/current smoking, pack-years or intensity when available, alcohol history | Smoking as predictor; alcohol as exclusion/adjudication |
| Imaging/procedure/pathology | Label and adjudication evidence | Radiology, hepatology, procedure, pathology systems | FibroScan/VCTE, CAP, MRE, MRI-PDFF, biopsy fibrosis stage, cirrhosis morphology | Labels, not predictors |
| Clinical notes | Auxiliary abstraction/adjudication | Approved note abstraction pipeline or chart review | Alcohol history, viral hepatitis exclusions, MASH diagnosis, imaging mentions, biopsy/FibroScan evidence, smoking/family history when not structured | Adjudication and field completion, not unrestricted model text |

### Real-World Temporal Alignment

For deployment, each prediction has an index date. Predictors come from a pre-index lookback window, ideally the most recent valid value in the prior 6-12 months. Labels occur at or after the index date. This mirrors how the model would be used in practice: flag the patient before the liver workup happens.

---

## 3. Prototype / Hackathon Data Sources

| Dataset | Size Used | Why Selected | Limitations |
|---|---:|---|---|
| NHANES 2017-March 2020 | 15,560 rows loaded; 9,693 adults; 7,768 adults with complete elastography; 4,827 CKM-primary labeled adults before alcohol exclusion; 4,645 final primary cohort after alcohol exclusion | Public, de-identified, contains routine labs/vitals, CKM markers, medication data, smoking/alcohol questionnaires, VCTE liver stiffness, and CAP | Cross-sectional, not a hospital EHR, lacks biopsy-confirmed MASH for most participants |
| Dyania synthetic clinical notes examples | 2 note sections parsed from supplied examples | Demonstrates how note-derived evidence can support field abstraction, exclusions, and audit labels | Notes contain explicit answers/staging, so they are not primary training inputs |
| UK Biobank imaging/EHR | Future external validation only | Large-scale imaging, labs, metabolic traits, and longitudinal outcomes | Access-controlled and not feasible as primary hackathon derivation data |

### NHANES Files Used

| File | Role |
|---|---|
| `P_DEMO` | Demographics, age, sex, race/ethnicity, survey weights |
| `P_LUX` | Liver ultrasound transient elastography and CAP labels |
| `P_BMX` | BMI and waist circumference |
| `P_BPXO` | Blood pressure |
| `P_BIOPRO` | Albumin, bilirubin, ALP, AST, ALT, creatinine, glucose |
| `P_CBC` | Platelets and hemoglobin |
| `P_GHB` | HbA1c |
| `P_HDL`, `P_TCHOL`, `P_TRIGLY` | Lipids |
| `P_DIQ`, `P_BPQ`, `P_MCQ` | Diabetes, blood pressure/cholesterol history, cardiovascular history |
| `P_ALQ` | Alcohol-use exclusion |
| `P_SMQ` | Smoking status and intensity fields |
| `P_RXQ_RX` | Medication name and reason-text cross-checks |

---

## 4. Cohort Definitions

### Primary Prototype Cohort

The primary NHANES model cohort includes:

- Adults aged 18 years or older.
- Complete elastography label.
- CKM-primary enrichment: diabetes or at least two CKM markers.
- No substantial alcohol-use pattern by sex-specific questionnaire-derived thresholds.

This cohort is chosen because the Dyania task is not population screening; it is risk stratification in patients with compounding metabolic conditions.

### Sensitivity Cohorts

Sensitivity analyses include:

- CKM-any cohort: at least one CKM marker.
- Stricter endpoint: same held-out patients evaluated against LSM >=12.0 kPa.
- Diabetes subgroup.
- Obesity plus diabetes subgroup.
- High CKM burden subgroup.
- Ever-smoker and never-smoker subgroups.
- NHANES-weighted descriptive and model-sensitivity analysis.

The same-model subgroup analyses use locked held-out XGBoost predictions. The model is not refit for every subgroup, which avoids cherry-picking.

---

## 5. Label Construction

### Prototype Label

The primary prototype label is:

```text
Complete elastography exam and VCTE liver stiffness measurement >= 8.0 kPa
```

This is a sensitive proxy for clinically significant MASH/fibrosis risk in a CKM/metabolic-risk context. It is not presented as biopsy-confirmed MASH.

The stricter sensitivity label is:

```text
Complete elastography exam and VCTE liver stiffness measurement >= 12.0 kPa
```

CAP is used for steatosis context and phenotype stratification, not as the sole MASH label.

### Future EHR Label Hierarchy

1. Biopsy-confirmed MASH with fibrosis stage F2 or higher, or advanced fibrosis/cirrhosis.
2. MRE or VCTE consistent with clinically significant or advanced fibrosis.
3. Liver imaging plus hepatology-documented MASH/MASLD fibrosis assessment.
4. Physician-adjudicated chart review using structured fields and validated note abstraction.

FibroScan kPa, CAP, biopsy, imaging-confirmed fibrosis, and explicit MASH/fibrosis text are labels or adjudication evidence. They are not predictors.

---

## 6. Feature Harmonization

The harmonized dataset converts raw NHANES files into one row per participant.

Core derived fields include:

- FIB-4.
- APRI.
- NAFLD Fibrosis Score.
- AST/ALT ratio.
- eGFR.
- Obesity flag.
- Diabetes flag from questionnaire, HbA1c/glucose, and medication evidence.
- Hypertension flag from questionnaire, measured BP, and medication evidence.
- Dyslipidemia flag from questionnaire, lipids, and medication evidence.
- CKD flag.
- CVD history flag.
- Metabolic syndrome proxy.
- CKM burden count.
- Smoking-status flags: ever, never, current, former.
- Smoking intensity fields when available.
- Medication flags for diabetes, lipid therapy, and hypertension.

Medication fields are used as supportive evidence because treatment can reveal disease that diagnosis fields miss. For example, metformin, insulin, GLP-1 receptor agonists, and SGLT2 inhibitors support diabetes phenotype construction. Medication reason fields are used when available, but absence of reason text does not invalidate a clear medication-class signal.

---

## 7. Preprocessing and Data Quality

### Cleaning Rules

- Deduplicate by participant or patient identifier.
- Harmonize lab units before feature engineering.
- Recode impossible physiologic values to missing.
- Preserve plausible extreme elastography values when the exam is complete.
- Recode NHANES special missing values such as refused, don't know, or missing according to variable-specific coding.
- Keep alcohol questionnaire fields for exclusion/adjudication rather than prediction.
- Keep smoking as a risk-context predictor.

### Missing Data Strategy

Missing data are handled without complete-case deletion:

- Continuous features use training-set median imputation for models that require imputation.
- Missingness indicators are created for model features.
- Tree models are given missingness indicators and can learn from missing-value behavior.
- High-missingness features are retained only when clinically meaningful and paired with missingness indicators.
- MICE and SMOTE are not primary hackathon methods because they add complexity, can distort probability calibration, and are harder to defend quickly.

In the NHANES proof-of-concept run, the highest missingness among model features was smoking intensity: current cigarettes per day (84.4%), smoking days in the past 30 days (84.0%), and smoking age started (57.9%). This is expected because intensity fields apply mainly to smokers/current smokers. LDL (53.0%) and triglycerides (52.5%) were also frequently missing due to fasting/lipid-subset availability. Core liver-score variables had lower missingness: FIB-4/APRI 5.8%, AST 5.7%, ALT 5.3%, and platelets through FIB-4/APRI 5.8%.

---

## 8. Leakage Controls

The main data risk is label leakage. The plan prevents it through the following rules:

- Do not use FibroScan/VCTE, CAP, MRE, MRI-PDFF, biopsy, explicit MASH diagnosis, or explicit fibrosis stage as predictors.
- Use those fields only to define labels, adjudicate endpoints, or audit demo examples.
- Do not use free-text note embeddings as primary predictors in the prototype.
- Use note extraction only to demonstrate how structured fields, exclusions, and label evidence could be abstracted in production.
- In hospital validation, require predictors to occur before the index date and labels after the index date.
- Select thresholds on validation data only; report final metrics on the untouched test set.

These rules are especially important because the supplied Dyania notes contain direct staging evidence such as FibroScan and biopsy findings. That information is clinically valuable, but using it as an input to predict MASH risk would make the task trivial and invalid.

---

## 9. Prototype Data Outputs

The implemented pipeline writes the following reproducible artifacts:

| Artifact | Purpose |
|---|---|
| `data/processed/nhanes_mash_model.csv` | Harmonized participant-level modeling dataset |
| `artifacts/cohort_summary.json` | Cohort counts, endpoint prevalence, missingness, medication agreement |
| `artifacts/model_comparison.md` | Model comparison table across baselines, ElasticNet, XGBoost, LightGBM, CatBoost |
| `artifacts/risk_tier_metrics.md` | Low/intermediate/high tier event rates and decision-rule metrics |
| `artifacts/sensitivity_analysis_same_model.md` | Same-model subgroup and stricter-endpoint sensitivity analyses |
| `artifacts/decision_curve_comparison.csv` | Decision Curve Analysis values |
| `artifacts/clinical_note_extracted_features.md` | Structured values extracted from the supplied note examples |
| `artifacts/clinical_note_demo_risk_cards.csv` | Note-demo risk predictions and leakage-control audit fields |
| `artifacts/*waterfall*.png` | SHAP-style patient explanation plots |

These artifacts are supportive evidence for the submission. The required deliverables remain the protocol, ML approach, data plan, README, and presentation.

---

## 10. Data Governance and Privacy

NHANES is public and de-identified. Real EHR deployment would require:

- IRB review or exemption determination.
- Data-use agreements.
- Minimum necessary data extraction.
- Secure storage and access controls.
- Audit logs for model access and clinician review.
- HIPAA/GDPR-aligned handling where applicable.
- Site-contained processing for PHI.
- Clinician override and documentation workflows.

The model is clinical decision support, not autonomous diagnosis. All risk outputs should be reviewable, explainable, logged, and monitored after deployment.

---

## 11. Why This Data Strategy Fits Dyania

Dyania's strength is extracting clinically meaningful evidence from complex EMRs. This data plan uses that strength without confusing abstraction with prediction:

- Structured data drives early risk scoring.
- Notes improve exclusions, missing-field completion, and endpoint adjudication.
- Label-bearing note text is not allowed to leak into the model.
- High-risk patients can be surfaced for FibroScan, hepatology review, or trial pre-screening.
- Care gaps become measurable: missing platelet count, absent liver staging, no hepatology referral, or incomplete metabolic labs.

This makes the project both scientifically defensible and aligned with Dyania's real-world value proposition.
