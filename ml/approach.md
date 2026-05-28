# ML Approach

## 1. Problem Formulation

The machine-learning task is supervised risk prediction for clinically significant MASH/fibrosis among adults with cardiometabolic-kidney (CKM) risk factors. The model estimates a calibrated pre-test probability using data that should be available before confirmatory liver staging, then maps that probability into a clinician-actionable risk tier.

This is intentionally a MASH-first model, not a broad CKM disease model. CKM features define and explain the enriched population: diabetes, obesity, hypertension, dyslipidemia, CKD/eGFR impairment, cardiovascular disease, and cumulative CKM burden all help quantify why MASH/fibrosis risk arises. The prediction target remains liver disease risk.

The intended clinical question is:

> Among CKM-enriched adults who have not necessarily had FibroScan, biopsy, or hepatology review, who should be prioritized for liver staging or trial pre-screening?

The prototype uses NHANES 2017-March 2020 for derivation because it provides routine clinical variables plus liver ultrasound transient elastography. In a hospital setting, the same modeling structure would use longitudinal structured EHR data and note-derived abstraction only where validated.

---

## 2. Chosen Models and Justification

| Model | Role | Why We Included It | Decision |
|---|---|---|---|
| FIB-4 | Clinical baseline and engineered feature | Widely used first-line fibrosis risk score from age, AST, ALT, and platelets. The model must beat or complement this simple baseline. | Required comparator and model feature. |
| APRI | Clinical baseline and engineered feature | Simple AST/platelet fibrosis marker; useful when comparing against established low-complexity rules. | Required comparator and model feature. |
| NAFLD Fibrosis Score (NFS) | Clinical baseline and engineered feature | Incorporates age, BMI, diabetes, AST/ALT ratio, platelets, and albumin; clinically interpretable for metabolic liver disease risk. | Required comparator and model feature. |
| ElasticNet logistic regression | Transparent benchmark | Produces directionally interpretable coefficients/standardized odds ratios and protects against overfitting through regularization. | Kept as the main transparent sanity-check model. |
| XGBoost gradient-boosted trees | Primary presentation model | Captures nonlinear tabular relationships and interactions, such as diabetes plus obesity plus platelet/liver-enzyme patterns. Produces stable SHAP explanations and balanced triage behavior. | Selected as primary model. |
| LightGBM | Nonlinear challenger | Strong tabular model; included to test whether an alternative boosting implementation improves discrimination or calibration. | Performed well, but selected a more conservative high-risk threshold with lower sensitivity. |
| CatBoost | Nonlinear challenger | Robust tabular model; included because it can perform well on heterogeneous clinical features. | Similar discrimination, not chosen over XGBoost for final presentation. |
| Calibration layer | Risk-score layer | Converts raw model outputs into probabilities that can be responsibly shown as percentages. | Required before presenting risk. |
| SHAP explanations | Explanation layer | Shows patient-level factor contributions and global feature importance. | Used for clinician-facing grouped explanations and audit waterfalls. |

### Why XGBoost Is Primary

LightGBM achieved the strongest AUPRC and Brier score in the current run, but the selected LightGBM high-risk threshold was more conservative and produced lower high-risk sensitivity. The goal of this product is not only ranking, but earlier triage. XGBoost offered the best balance between discrimination, calibration, sensitivity at clinically useful thresholds, Decision Curve Analysis net benefit, and explainability artifacts. For that reason, XGBoost is used as the primary model for the submission and patient-card examples.

### Why We Did Not Use a Deep Learning or LLM Model

The primary prediction problem is structured tabular risk stratification with a modest derivation cohort. A deep neural network would add complexity without clear advantage and would make calibration and explanation harder. Clinical notes are valuable for abstraction and adjudication, but the Dyania examples contain explicit staging and diagnosis text; using them as unrestricted predictors would leak the answer. This also aligns with Dyania's Synapsis AI framing: note abstraction can populate evidence fields, while our model supplies a disease-specific MASH risk protocol and action thresholds.

---

## 3. Feature Engineering

### Structured Input Variables

The model uses variables that are routinely available in primary care, endocrinology, cardiology, nephrology, or metabolic clinics:

- Demographics: age, sex, race/ethnicity where available for monitoring and fairness review.
- Anthropometrics and vitals: BMI, waist circumference, systolic blood pressure, diastolic blood pressure.
- Liver labs: AST, ALT, alkaline phosphatase, bilirubin, albumin.
- Hematology: platelets and hemoglobin.
- Metabolic labs: HbA1c, glucose, HDL, LDL, total cholesterol, triglycerides.
- Kidney markers: creatinine and eGFR; urine albumin/creatinine is planned for EHR deployment when available.
- CKM diagnoses/phenotypes: diabetes, prediabetes, obesity, hypertension, dyslipidemia, CKD, CVD.
- Smoking history: ever, never, current, former, and intensity fields where available.
- Medication evidence: diabetes medications, lipid medications, antihypertensives, and overall medication evidence.

### Engineered Features

| Feature | Derivation | Rationale |
|---|---|---|
| FIB-4 | Age x AST / (platelets x sqrt(ALT)) | Encodes an accepted fibrosis triage heuristic and provides a strong clinical baseline. |
| APRI | (AST / upper limit normal) / platelets x 100 | Adds another simple fibrosis signal based on liver injury and platelet pattern. |
| NAFLD Fibrosis Score | Age, BMI, diabetes, AST/ALT ratio, platelets, albumin | Adds a metabolic-liver fibrosis score that is interpretable to clinicians. |
| AST/ALT ratio | AST divided by ALT | Captures liver-injury pattern and potential advanced disease signal. |
| eGFR / CKD flag | CKD-EPI-style eGFR proxy and thresholded CKD flag | Captures kidney dimension of CKM syndrome and overall metabolic severity. |
| Metabolic syndrome proxy | Obesity/waist, dyslipidemia, hypertension, glycemic abnormality | Captures clustering of metabolic dysfunction. |
| CKM burden count | Count of diabetes, obesity, hypertension, dyslipidemia, CKD, CVD | Summarizes cumulative risk burden in a clinically readable way. |
| Medication class flags | Text-matched medication and reason fields | Cross-checks diagnoses and captures treated disease when diagnosis fields are incomplete. |
| Missingness indicators | One indicator per model feature | Preserves information about care gaps and avoids complete-case deletion. |

### Variables Excluded to Avoid Label Leakage

The model does not use FibroScan/VCTE liver stiffness, CAP, biopsy result, MRI/MRE fibrosis evidence, explicit MASH stage, or explicit fibrosis diagnosis text as predictors. These are labels or adjudication evidence. In the clinical-note examples, these fields are displayed only for demo audit and are not used to generate the risk score.

Alcohol history, viral hepatitis, and competing liver-disease evidence are handled as exclusion/adjudication fields rather than primary predictors. Smoking is treated differently: it is a risk-context variable, not a competing diagnosis, so it is included when available.

---

## 4. Missing Data and Data Quality

Missingness is handled as part of the model rather than by discarding patients. This matters clinically because incomplete fibrosis workup is itself part of the care gap.

The prototype approach is:

- No complete-case filtering except required adult age and valid elastography label for supervised NHANES derivation.
- Median imputation and missingness indicators for ElasticNet and clinical baselines.
- XGBoost/LightGBM/CatBoost trained with missingness indicators; tree models can also learn split behavior around missing values.
- Impossible physiologic values are recoded to missing rather than forcing unrealistic values into the model.
- MICE and SMOTE are not primary hackathon methods because they add complexity, can distort calibration, and are harder to explain under time constraints.

In the current NHANES run, the highest missingness was in smoking intensity variables and fasting lipid variables. That is expected: intensity questions apply mainly to smokers/current smokers, and LDL/triglycerides are often measured in fasting subsamples. We retain these fields with missingness indicators instead of deleting half the cohort.

---

## 5. Label Definition

### Prototype Label

The primary NHANES prototype label is:

```text
Complete elastography exam and liver stiffness measurement (LSM) >= 8.0 kPa
```

This threshold is used as a sensitive proxy for clinically significant fibrosis risk in a CKM/metabolic-risk context. The sensitivity endpoint is:

```text
Complete elastography exam and LSM >= 12.0 kPa
```

The higher threshold enriches for more advanced fibrosis and tests whether the same model identifies more severe disease on the same held-out patients.

### Future EHR Label Hierarchy

For hospital validation, the label hierarchy should be:

1. Biopsy-confirmed MASH with fibrosis stage F2 or higher, or advanced fibrosis/cirrhosis.
2. MRE/VCTE evidence consistent with clinically significant or advanced fibrosis.
3. Imaging plus hepatology assessment consistent with MASH/MASLD fibrosis.
4. Physician-adjudicated chart review using structured data and note-derived evidence.

Predictors must come from a pre-index window; labels must occur at or after the index date.

---

## 6. Validation Strategy

### Data Split

The pre-specified validation rule was:

- If the CKM-labeled cohort has at least 2,000 participants and at least 200 positives, use a 60% train, 20% validation/calibration, 20% untouched test split.
- If smaller, use 80% development and 20% untouched test, with cross-validation inside development.

NHANES met the larger-cohort rule. The final prototype cohort contained 4,645 CKM-primary labeled adults after alcohol exclusion, including 646 positives at LSM >=8.0 kPa and 222 positives at LSM >=12.0 kPa. The pipeline therefore used a stratified 60/20/20 participant-level split.

### Model Selection

Models were compared on:

- AUROC: overall ranking ability.
- AUPRC: ability to find true positives when positives are less common.
- Brier score: probability accuracy.
- Calibration slope and intercept: whether risk percentages are usable.
- Sensitivity and NPV at the rule-down threshold.
- PPV, specificity, and referral yield at the high-risk threshold.
- Decision Curve Analysis (DCA): net clinical benefit compared with FIB-4, screen-all, and screen-none.
- Subgroup behavior: diabetes, obesity plus diabetes, high CKM burden, smoking history.
- Interpretability: ability to produce clinician-usable factor contributions.

The final test set was untouched until model selection, calibration, and thresholds were selected on training/validation data.

### Class Imbalance

The primary models use class weighting rather than accuracy optimization. Accuracy is not a suitable target because a model can look accurate by under-referring patients in a relatively low-prevalence setting. Thresholds are selected around clinical action: safe rule-down for low risk, and enriched referral yield for high risk.

### Sample Weights

NHANES sample weights are used for descriptive prevalence and sensitivity analysis, not as the primary training objective. This is deliberate: the goal is hospital triage performance in CKM-enriched care pathways, not national population prevalence estimation.

---

## 7. Proof-of-Concept Results

### Cohort Flow

| Step | N |
|---|---:|
| Total NHANES rows loaded | 15,560 |
| Adults | 9,693 |
| Adults with complete elastography | 7,768 |
| CKM-primary labeled adults before alcohol exclusion | 4,827 |
| Excluded for substantial alcohol-use pattern | 182 |
| Final CKM-primary labeled cohort | 4,645 |
| LSM >=8.0 kPa positives | 646 |
| LSM >=12.0 kPa positives | 222 |
| Held-out test set | 929 |
| Held-out test positives at LSM >=8.0 kPa | 129 |

### Model Comparison on Held-Out Test Set

| Model | AUROC | AUPRC | Brier | High-risk sensitivity | Specificity | PPV | NPV |
|---|---:|---:|---:|---:|---:|---:|---:|
| FIB-4 | 0.569 | 0.197 | 0.119 | 0.279 | 0.779 | 0.169 | 0.870 |
| APRI | 0.605 | 0.246 | 0.115 | 0.310 | 0.841 | 0.240 | 0.883 |
| NFS | 0.697 | 0.278 | 0.112 | 0.419 | 0.848 | 0.307 | 0.900 |
| ElasticNet | 0.803 | 0.379 | 0.100 | 0.674 | 0.775 | 0.326 | 0.937 |
| XGBoost | 0.804 | 0.444 | 0.098 | 0.667 | 0.784 | 0.332 | 0.936 |
| LightGBM | 0.802 | 0.464 | 0.094 | 0.481 | 0.901 | 0.440 | 0.915 |
| CatBoost | 0.805 | 0.432 | 0.098 | 0.620 | 0.809 | 0.343 | 0.930 |
| Weighted XGBoost sensitivity | 0.791 | 0.403 | 0.099 | 0.434 | 0.912 | 0.444 | 0.909 |

XGBoost was selected for the product narrative because it balances sensitivity, calibration, referral yield, DCA, and explanation quality. LightGBM's higher AUPRC and Brier score are noted, but its high-risk threshold produced fewer immediate escalations.

### Final XGBoost Performance

- AUROC: 0.804.
- AUPRC: 0.444.
- Brier score: 0.098.
- Bootstrap AUROC 95% CI: 0.766-0.842.
- Calibration intercept: approximately 0.000.
- Calibration slope: 1.031.
- High-risk cutoff: 24.4%.
- High-risk direct referral: sensitivity 66.7%, specificity 78.4%, PPV 33.2%, NPV 93.6%.
- Escalating all intermediate/high patients above the low-risk cutoff: sensitivity 94.6%, NPV 97.8%.

### Risk Tiers

| Tier | Probability Range | Intended Action |
|---|---|---|
| Low | <8.3% | Routine monitoring and cardiometabolic optimization. |
| Intermediate | 8.3% to <24.4% | Complete missing labs, repeat/confirm FIB-4, consider ELF or FibroScan depending local capacity. |
| High | >=24.4% | Prioritize FibroScan/VCTE, hepatology review, or MASH trial pre-screening. |

On the held-out test set, observed positive rates rose across predicted-risk strata:

- Low tier: 2.2%.
- Intermediate tier: 10.2%.
- High tier: 33.2%.
- Highest predicted-risk quartile: 34.5%.

This supports the clinical use case: the model is not simply producing a score; it enriches a worklist for patients more likely to have clinically meaningful liver stiffness.

---

## 8. Same-Model Sensitivity Analyses

Sensitivity analyses use the locked XGBoost predictions on the same held-out patients. The model is not refit for each subgroup or endpoint, which avoids cherry-picking.

| Analysis | Endpoint | N | Sensitivity | Specificity | PPV | NPV |
|---|---|---:|---:|---:|---:|---:|
| Primary high-risk referral | LSM >=8 | 929 | 0.667 | 0.784 | 0.332 | 0.936 |
| Escalate above low cutoff | LSM >=8 | 929 | 0.946 | 0.389 | 0.200 | 0.978 |
| Stricter endpoint | LSM >=12 | 929 | 0.755 | 0.748 | 0.143 | 0.982 |
| Diabetes subgroup | LSM >=8 | 278 | 0.828 | 0.598 | 0.381 | 0.921 |
| Obesity plus diabetes subgroup | LSM >=8 | 156 | 0.898 | 0.430 | 0.419 | 0.902 |
| High CKM burden subgroup | LSM >=8 | 526 | 0.722 | 0.690 | 0.345 | 0.916 |
| Ever-smoker subgroup | LSM >=8 | 401 | 0.630 | 0.761 | 0.291 | 0.930 |
| Never-smoker subgroup | LSM >=8 | 527 | 0.693 | 0.801 | 0.366 | 0.940 |

The subgroup results should be interpreted as feasibility checks rather than final clinical claims. They suggest that the model remains clinically useful in diabetes, obesity-plus-diabetes, and high-CKM-burden groups, but subgroup calibration would need prospective validation before deployment.

---

## 9. Expected Model Output

The product output is designed as a clinician-facing risk card rather than a black-box score.

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

The default view shows:

- Calibrated risk percentage.
- Low/Intermediate/High action tier.
- Recommended next step.
- Top grouped contributors.
- Missing-data/care-gap flags.

The audit view shows:

- Full SHAP waterfall plot.
- Feature-level values used by the model.
- Whether the patient was positive or negative by adjudicated staging evidence.
- Leakage-control note confirming that FibroScan, biopsy, and explicit MASH text were not used as predictors.

In the supplied Dyania note examples, the parser extracts structured values such as age, BMI, AST, ALT, platelets, albumin, HbA1c, lipids, CKM markers, medication evidence, and smoking status. It records FibroScan/biopsy/fibrosis-stage evidence only as audit labels.

---

## 10. Clinical Integration

The model is intended to run as an EHR-integrated pre-screening layer:

1. Identify CKM-enriched adults from structured EHR data.
2. Pull the most recent valid pre-index labs, vitals, diagnoses, medications, and smoking history.
3. Calculate FIB-4, APRI, NFS, AST/ALT ratio, CKM burden, and missingness flags.
4. Generate a calibrated MASH/fibrosis probability.
5. Assign a clinical action tier.
6. Route high-risk patients to FibroScan/hepatology/trial pre-screening.
7. Route intermediate-risk patients to missing-lab completion, repeat scoring, ELF, or FibroScan depending capacity.
8. Log clinician action and downstream staging outcome for model monitoring.

This complements Dyania's EMR abstraction strengths. Synapsis-style abstraction can identify evidence buried in notes; CKM-MASH Sentinel turns structured and validated abstracted fields into a disease-specific risk score and operational triage pathway.

---

## 11. Limitations and Failure Modes

- NHANES is cross-sectional, so same-cycle labs and elastography cannot prove longitudinal prediction.
- The prototype label is elastography-based fibrosis risk, not biopsy-confirmed MASH.
- VCTE thresholds can vary by disease context, inflammation, congestion, and technical factors.
- Alcohol-related liver disease, viral hepatitis, autoimmune liver disease, pregnancy, transplant history, or acute hepatitis can make routine risk scores misleading.
- Smoking intensity variables are sparse in NHANES and need careful missingness handling.
- Family history, sedentary lifestyle, clinical signs, longitudinal referrals, and prior care gaps are important ideal EHR fields but are not consistently available in the NHANES prototype.
- XGBoost risk percentages require calibration and site-level monitoring before clinical use.
- SHAP explains model behavior but does not establish causality.

---

## 12. Why This Fits the Dyania Track

The Dyania challenge asks for earlier detection of MASH risk in patients with compounding metabolic conditions. This approach is aligned because it:

- Surfaces patients earlier, before liver disease is already obvious.
- Uses routine structured data that hospitals already have.
- Avoids leakage from explicit diagnosis/staging text.
- Supports clinical-trial pre-screening by enriching the high-risk worklist.
- Produces auditable explanations for clinicians.
- Treats missingness and absent staging as care-gap signals.
- Leaves room for Dyania/Synapsis-style note abstraction to improve adjudication and deployment.

The result is not just an ML benchmark. It is a deployable clinical workflow: identify the right CKM patients, estimate calibrated MASH/fibrosis risk, explain the drivers, and recommend the next clinical action.
