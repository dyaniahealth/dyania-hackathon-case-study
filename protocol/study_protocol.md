# Study Protocol

## 0. Problem Statement and Approach

### Problem Statement

Metabolic dysfunction-associated steatohepatitis (MASH) is a progressive liver disease that commonly coexists with obesity, type 2 diabetes, cardiovascular disease, and chronic kidney disease under the broader cardiometabolic-kidney (CKM) spectrum. Early disease is often clinically silent, nonspecific, or attributed to isolated metabolic conditions rather than recognized as part of a multisystem process. Patients can therefore move between primary care, endocrinology, cardiology, nephrology, and metabolic clinics without a coordinated liver-risk assessment.

This creates a detection gap: high-risk patients are often identified only after abnormal imaging, elastography, hepatology referral, or advanced complications. Delayed identification increases the risk of advanced fibrosis/cirrhosis, cardiovascular events, CKD progression, avoidable hospitalizations, missed therapeutic windows, and missed clinical-trial recruitment opportunities.

### Our Approach

CKM-MASH Sentinel is an explainable EHR-integrated pre-screening protocol that passively identifies CKM-enriched adults at elevated risk for undiagnosed clinically significant MASH/fibrosis. The system does not replace clinical diagnosis. It uses routinely available pre-index structured data to estimate calibrated pre-test probability, classify patients into Low / Intermediate / High action tiers, and recommend the next clinical step.

The full hospital-ready version would run continuously across primary care, endocrinology, cardiology, nephrology, hepatology, and metabolic clinics. It would support FibroScan referral, hepatology review, intensified cardiometabolic management, nephrology/cardiology follow-up where appropriate, and MASH clinical-trial pre-screening.

The hackathon proof of concept is intentionally narrower: it uses NHANES 2017-March 2020 to predict elevated liver stiffness measurement (LSM >=8.0 kPa) in CKM-enriched adults using structured routine variables. This demonstrates feasibility while avoiding claims of clinical readiness.

---

## 1. Study Title and Objectives

**Title:**  
CKM-MASH Sentinel: An Explainable Structured-EHR Pre-Screening Study for Early MASH/Fibrosis Risk Stratification in Adults With Cardiometabolic-Kidney Risk Factors.

**Primary Objective:**  
Develop and validate an explainable structured-data model that identifies CKM-enriched adults at elevated risk for undiagnosed clinically significant MASH/fibrosis before confirmatory liver elastography, imaging, biopsy, or hepatology referral.

**Secondary Objectives:**

- Evaluate whether model-assigned risk improves referral enrichment compared with FIB-4, APRI, NAFLD Fibrosis Score, liver-enzyme-based screening, screen-all, and screen-none strategies.
- Evaluate whether baseline model-assigned risk correlates with future adverse outcomes in a full-scale EHR study, including advanced fibrosis, cirrhosis, CKD progression, cardiovascular events, hospitalization, and mortality.
- Evaluate whether the system can function as an upstream triage layer that prioritizes high-risk patients for non-invasive fibrosis assessment, specialist referral, intensified metabolic management, or clinical-trial eligibility review.
- Evaluate feasibility of continuous EHR-integrated deployment across real-world primary care, endocrinology, cardiology, nephrology, hepatology, and metabolic workflows.
- Identify CKM subgroups where MASH/fibrosis risk is concentrated, including diabetes, obesity, dyslipidemia, CKD, ASCVD, smoking history, and high CKM-burden patients.
- Evaluate whether informative missingness and absent liver staging reveal care gaps that can be closed through automated pre-screening.
- Compare the proposed CKM-aware model against liver-focused models that do not explicitly account for kidney, cardiovascular, and metabolic interactions.
- Define a clinician-facing output: calibrated risk score, Low / Intermediate / High action tier, recommended next step, and grouped feature-contribution explanation.

---

## 2. Target Population

### Target Population

Adults at elevated CKM risk who may have undiagnosed MASH or progressive liver fibrosis, identified using routine clinical and laboratory data from primary care, endocrinology, cardiology, nephrology, hepatology, and metabolic clinics.

### Inclusion Criteria

#### Hackathon Derivation / Prototype Cohort

- Adults aged 18 years or older in NHANES 2017-March 2020.
- Valid liver ultrasound transient elastography quality status and available liver stiffness measurement.
- Routine structured variables available before or at the same NHANES examination cycle, including age, sex, BMI, AST, ALT, platelet count, glucose or HbA1c, lipids, creatinine/eGFR, blood pressure, medications, and smoking history when available.
- Primary CKM enrichment defined as either diabetes or at least two CKM markers among obesity, hypertension, dyslipidemia, reduced eGFR/CKD marker, or cardiovascular disease history.
- Sensitivity cohort defined as at least one CKM marker.

#### Ideal Real-World EHR Cohort

- Adults aged 18 years or older.
- At least 12 months of pre-index EHR history.
- Presence of at least two CKM-related factors, operationalized as any two of:
  - BMI >=25 kg/m2 or waist circumference >102 cm in men / >88 cm in women.
  - HbA1c >=5.7%, type 2 diabetes diagnosis, or use of anti-diabetic medication.
  - Systolic BP >=130 mmHg, diastolic BP >=80 mmHg, hypertension diagnosis, or anti-hypertensive medication use.
  - Triglycerides >=150 mg/dL, HDL <40 mg/dL in men / <50 mg/dL in women, dyslipidemia diagnosis, or lipid-lowering medication use.
  - eGFR <60 mL/min/1.73 m2 by CKD-EPI 2021 or albuminuria/UACR >=30 mg/g.
  - ASCVD history, including prior myocardial infarction, stroke, coronary revascularization, angina, or EHR-documented cardiovascular disease.
- Availability of ALT, AST, and platelet count for FIB-4 calculation in the pre-index window, unless the model is being run specifically to detect missing-data care gaps.
- At least one confirmatory or adjudication source after the pre-index feature window for validation: FibroScan/VCTE, CAP, ELF, MRI-PDFF, MRE, biopsy, hepatology assessment, or physician-adjudicated chart review.

### Exclusion Criteria

Participants will be excluded from derivation or endpoint validation if any of the following are present:

- Known cirrhosis, hepatic decompensation, active hepatocellular carcinoma, prior liver transplantation, or pregnancy at index.
- Active HBV or HCV, autoimmune liver disease, Wilson disease, hemochromatosis, drug-induced liver injury, primary biliary cholangitis, primary sclerosing cholangitis, or other documented chronic liver disease likely to confound fibrosis assessment.
- Known alcohol-associated liver disease or substantial alcohol intake above site-defined MASLD/MASH eligibility thresholds.
- Missing essential information required for endpoint definition, validation, or safe interpretation after reasonable imputation/missingness handling.
- Direct label leakage sources in the prediction window, including explicit post-index MASH stage, biopsy result, FibroScan kPa, CAP score, MRI/MRE fibrosis evidence, or imaging-confirmed fibrosis.

### Cohort Size Estimate

For the hackathon prototype, all eligible NHANES 2017-March 2020 adults meeting age, CKM enrichment, elastography-label, and alcohol-exclusion criteria are used. The implemented cohort included 4,645 CKM-primary adults after high-alcohol exclusion, including 646 participants with LSM >=8.0 kPa and 222 with LSM >=12.0 kPa.

For full-scale hospital development, the proposed initial development cohort is approximately 10,000-15,000 unique CKM-enriched patients from historical EHR data. Assuming approximately 50 candidate predictors and a target of 15-20 MASH-positive cases per effective predictor, at least 750-1,000 MASH-positive cases would support stable model training, internal validation, and calibration.

An independent external validation cohort of approximately 20,000-50,000 patients would assess transportability across sites, demographic groups, and data-capture patterns. A larger longitudinal prognostic cohort of approximately 100,000-250,000 patients with 3-5 years of follow-up would evaluate downstream outcomes such as cirrhosis, liver-related hospitalization, CKD progression, MACE, dialysis, and mortality.

---

## 3. Endpoints

### Primary Endpoint

The primary endpoint is clinically significant MASH/fibrosis risk. In the hackathon prototype, this is operationalized as CKM/metabolic dysfunction plus valid VCTE liver stiffness measurement at or above 8.0 kPa, selected as a sensitive fibrosis-risk proxy for upstream pre-screening. A stricter sensitivity endpoint uses LSM >=12.0 kPa for advanced fibrosis enrichment.

In real EHR deployment, ground truth follows a hierarchical diagnostic definition:

1. Histopathologic confirmation: biopsy-confirmed MASH with fibrosis stage F2 or higher, or biopsy-confirmed advanced fibrosis/cirrhosis.
2. Advanced elastography or imaging evidence: FibroScan/VCTE, MRE, MRI-PDFF, or validated imaging findings consistent with clinically significant fibrosis in metabolic context.
3. Validated non-invasive fibrosis biomarkers: ELF or equivalent validated fibrosis biomarker panels when available.
4. Clinical surrogate / adjudicated definition: established fibrosis scores, abnormal liver enzymes, metabolic dysfunction profile, imaging evidence of steatosis, exclusion of competing liver etiologies, and physician-adjudicated chart review.

FibroScan, CAP, biopsy, imaging-confirmed fibrosis, explicit MASH diagnosis text, and fibrosis-stage text are labels or adjudication evidence. They are not model predictors.

Clinically useful performance is defined by high sensitivity and high negative predictive value for the rule-down tier, improved referral yield in the high-risk tier, good calibration, and improved Decision Curve Analysis net benefit compared with FIB-4 alone.

### Secondary Endpoints

| Endpoint | Measurement | Timeframe |
|---|---|---|
| Advanced fibrosis enrichment | VCTE/MRE/biopsy threshold consistent with advanced fibrosis, analyzed separately from the sensitive primary threshold | At or after index label date |
| Prognostic clinical outcomes | Incidence of advanced fibrosis/cirrhosis, liver-related hospitalization, rapid eGFR decline, eGFR <30, albuminuria >=300 mg/24h or equivalent, dialysis initiation, myocardial infarction, stroke, heart failure hospitalization, all-cause mortality | 3-5 years |
| Referral yield | Percent of model-flagged high-risk patients with positive elastography, imaging, biopsy, or adjudicated MASH/fibrosis evidence | Within 6-12 months after risk score |
| Care-gap closure | High-risk patients with no prior liver staging, no hepatology referral, or missing essential fibrosis-risk labs | Baseline and follow-up audit window |
| Trial pre-screening enrichment | Percent and number needed to screen to identify potentially eligible MASH/fibrosis candidates | At cohort screening / deployment |
| Comparative model performance | AUROC, AUPRC, calibration, sensitivity, specificity, NPV, PPV, F1, DCA versus FIB-4/APRI/NFS and liver-focused models | Internal and external validation |
| Calibration and clinical utility | Calibration slope/intercept, Brier score, DCA net benefit, risk-tier event rates | Held-out test set and external validation |

---

## 4. Proposed Data Sources

| Source | Data Type | Access Pathway | Key Variables |
|---|---|---|---|
| NHANES 2017-March 2020 | Public cross-sectional survey, labs, exam data, elastography, alcohol/smoking questionnaires, medication files | CDC public-use files | Age, sex, BMI, waist, AST, ALT, platelets, HbA1c/glucose, lipids, creatinine/eGFR, BP, diabetes markers, smoking history, medication flags, alcohol-use exclusion, VCTE LSM, CAP |
| Electronic Health Records | Structured longitudinal clinical and follow-up data | FHIR/HL7 APIs, OMOP, data warehouse, or site-approved extract | Demographics, ICD-10 diagnoses, comorbidities, encounters, medication exposure, referrals, hospitalizations, longitudinal outcomes |
| Laboratory Information Systems | Biochemical and hematologic labs | Automated laboratory feed / EHR integration | AST, ALT, platelet count, HbA1c, glucose, creatinine, eGFR, albuminuria/UACR, triglycerides, HDL, LDL, total cholesterol, bilirubin, albumin, GGT, uric acid |
| Vital Signs and Anthropometric Records | Physiologic and body-composition measurements | Structured outpatient and inpatient records | BP, heart rate, BMI, height, weight, waist circumference, waist-to-height ratio, waist-to-hip ratio where available |
| Imaging and Elastography Systems | Liver imaging and fibrosis assessment | PACS/radiology integration and elastography reports | Ultrasound findings, CT findings, FibroScan LSM, CAP, MRI-PDFF, MRE. These are labels/adjudication, not predictors, unless used only to define prior completed staging as a care pathway variable before index. |
| Histopathology Databases | Liver biopsy and pathology assessment | Pathology systems and biopsy registries | Steatosis grade, ballooning, lobular inflammation, fibrosis stage, NAS score |
| Administrative and Claims Databases | Utilization and events | Claims linkage / administrative registries | Hospitalizations, cardiovascular events, liver-related admissions, procedures, dialysis initiation, transplant, mortality |
| Clinical Notes | Auxiliary abstraction and adjudication | Dyania/Synapsis-style EMR abstraction or chart review | Alcohol history, smoking history if absent from structured fields, viral hepatitis exclusions, family history/liver disease context, imaging mentions, biopsy/FibroScan evidence, clinician assessment |
| UK Biobank | Future external validation | Access-controlled research application | Imaging-derived liver fat/fibrosis proxies, labs, diagnoses, metabolic traits |
| Optional Future Data Streams | Longitudinal monitoring and precision-risk refinement | Wearables, patient-reported outcomes, research biomarkers | Activity, sleep, weight trajectories, patient-reported symptoms, multi-omic biomarkers where governance permits |

**Ground Truth Definition:**  
Patients are classified according to the highest-quality evidence available in the endpoint hierarchy. Because biopsy is not routinely available in large-scale EHR populations, validated non-invasive and adjudicated surrogate definitions are included for real-world scalability. The model itself predicts risk before confirmatory testing.

---

## 5. Statistical Analysis Plan

### Sample Size

The hackathon prototype uses the eligible NHANES cohort described above. Missing structured predictors are handled with imputation and missingness indicators rather than complete-case deletion.

The full-scale EHR development cohort should include 10,000-15,000 patients with enough endpoint-positive cases to support 15-20 events per effective predictor. Clinically significant MASH/fibrosis prevalence is expected to be lower than broad MASLD prevalence but enriched in CKM populations, plausibly around 7-15% depending on endpoint definition and care setting. External and longitudinal cohorts should be larger to support subgroup calibration, transportability assessment, and lower-frequency downstream outcomes.

### Train / Validation / Test Split

The NHANES proof of concept uses a stratified participant-level 60% training, 20% validation/calibration, and 20% held-out test split because the labeled cohort exceeded the pre-specified threshold of 2,000 participants and 200 positives. The training set fits candidate models, the validation set supports hyperparameter tuning, calibration, and action-threshold selection, and the held-out test set is used only for final reporting.

For EHR deployment, a temporal split is preferred: train on earlier index dates, tune on later dates, and test on the most recent held-out period or a distinct site. Predictors must come from a pre-index lookback window, preferably the most recent valid value in the prior 6-12 months. Labels must occur at or after the index date.

Leakage controls:

- Split at the patient level before model fitting.
- Fit preprocessing, imputation, scaling, and feature selection only on training data.
- Derive predictors only from information available before the prediction index date.
- Exclude future labs, diagnoses, procedures, referrals, imaging, elastography, biopsy, and outcomes from predictors.
- Treat FibroScan, CAP, biopsy, MRI/MRE, explicit MASH text, and fibrosis stage as labels/adjudication evidence only.

### Candidate Models

| Model | Role | Justification |
|---|---|---|
| FIB-4, APRI, NAFLD Fibrosis Score | Clinical baselines and engineered features | Accepted non-invasive fibrosis-risk heuristics from routine variables. |
| ElasticNet logistic regression | Transparent benchmark | Provides coefficients/standardized odds ratios and clinical sanity checks. |
| XGBoost / LightGBM | Primary nonlinear tabular models | Strong performance on heterogeneous EHR data, nonlinear interactions, missingness handling, and SHAP compatibility. |
| CatBoost | Secondary comparator | Robust boosted-tree comparator for tabular clinical data. |
| Partial Least Squares | Future secondary comparator | Potentially useful for correlated metabolic/hepatic variables, inspired by MASHRisk-style approaches. |
| Random Forest | Future secondary comparator | Robust nonlinear ensemble comparator, but less preferred than calibrated boosting for final deployment. |

The implemented proof of concept benchmarks XGBoost, LightGBM, CatBoost, ElasticNet, sample-weighted XGBoost sensitivity, and calibrated FIB-4/APRI/NFS baselines. XGBoost is retained as the primary presentation model because it offers a strong triage balance, good calibration, DCA utility, and stable SHAP explanations.

### Feature Engineering

Raw structured inputs include:

- Demographics: age, sex, race/ethnicity where appropriate for fairness monitoring.
- Anthropometrics: BMI, waist circumference.
- Vitals: systolic and diastolic blood pressure.
- Liver biomarkers: AST, ALT, ALP, bilirubin, albumin, platelets; GGT where available in EHR.
- Metabolic biomarkers: HbA1c, glucose, triglycerides, HDL, LDL, total cholesterol; uric acid where available in EHR.
- Kidney biomarkers: creatinine, eGFR, albuminuria/UACR where available.
- Diagnoses/history: diabetes, obesity, hypertension, dyslipidemia, CKD, ASCVD/CVD, heart failure where available.
- Medication exposures: metformin, insulin, GLP-1 receptor agonists, SGLT2 inhibitors, statins, lipid therapies, antihypertensives.
- Social history: ever/current/former smoking and intensity measures where available.
- Care pathway signals: prior hepatology referral, prior FibroScan order, repeated abnormal liver enzymes, and missing key labs when temporally valid.

Engineered features include FIB-4, APRI, NAFLD Fibrosis Score, AST/ALT ratio, CKD stage/eGFR, metabolic syndrome proxy, CKM burden count, diabetes-control category, medication-intensity/supportive phenotype flags, and informative missingness indicators.

Correlation filtering, Boruta, LASSO, or PLS-style dimensionality reduction may be explored in a full EHR study. The hackathon prototype uses a clinically pre-specified feature set rather than aggressive automated feature selection to preserve interpretability and avoid overfitting under time constraints.

### Missing Data and Class Imbalance

Missingness is first characterized to distinguish unavailable-by-design variables from clinically informative care gaps. Continuous variables use training-set median imputation where clinically reasonable. Categorical variables use explicit missing/unknown categories or missingness indicators. Gradient boosting models may also use native missing-value behavior. Missingness indicators are retained for key labs and care-pathway fields.

MICE may be explored in a full-scale sensitivity analysis if missingness burden justifies it. It is not the primary hackathon approach because it adds complexity and may make calibration harder to defend quickly.

Class imbalance is handled through stratified splitting, class weighting, threshold optimization, sensitivity/NPV targets, referral-yield analysis, and DCA. SMOTE/ADASYN or hybrid methods such as SMOTE-Tomek may be evaluated only in full-scale model-development experiments; they are not primary for the hackathon pipeline because synthetic oversampling can distort clinical calibration.

### Evaluation Metrics

- Discrimination: AUROC and AUPRC.
- Rule-out safety: sensitivity and NPV at the Low-risk threshold.
- Referral utility: specificity, PPV, number needed to screen, and proportion of positive elastography/advanced fibrosis among High-risk patients.
- Calibration: Brier score, calibration slope/intercept, calibration curve, observed event rate by decile/quartile.
- Balanced performance: F1-score and confusion matrix at clinically selected thresholds.
- Clinical utility: Decision Curve Analysis comparing final model, FIB-4, APRI, NFS, screen-all, and screen-none strategies.
- Explainability: global feature importance and patient-level grouped SHAP contribution review.

Sensitivity and NPV are prioritized because the system is an upstream pre-screening tool where missed high-risk cases could delay diagnosis, specialist evaluation, and trial recruitment. Specificity and PPV are still evaluated to avoid excessive unnecessary referrals and imaging burden.

### Subgroup Analyses

Planned subgroup analyses include:

- Biological sex.
- Age categories: 18-39, 40-64, and >=65 years.
- BMI/metabolic burden: overweight, obesity, severe obesity, and increasing CKM burden.
- Diabetes status by HbA1c, diagnosis history, or anti-diabetic medication use.
- CKD stage / eGFR category.
- ASCVD status.
- Liver enzyme status: normal vs elevated AST/ALT.
- Smoking history.
- Race/ethnicity where available and ethically appropriate.

Subgroups are evaluated for AUROC, AUPRC, calibration, sensitivity, specificity, false-positive rates, and false-negative rates. Material subgroup divergence would trigger recalibration, threshold review, missingness review, and implementation constraints before deployment.

### Sensitivity Analyses

The primary analysis keeps one locked model and evaluates it on pre-specified patient subsets rather than refitting separate models for every inclusion rule. This prevents cherry-picking and preserves a fair comparison because all scenarios use the same feature definitions, calibration method, and held-out test predictions.

Planned sensitivity analyses:

- Broader CKM enrichment: at least one CKM marker instead of diabetes or at least two CKM markers.
- Stricter liver endpoint: VCTE LSM >=12.0 kPa for advanced fibrosis enrichment.
- Diabetes-enriched, obesity-plus-diabetes, high CKM-burden, ASCVD, CKD, liver-enzyme, and smoking-history subgroups.
- Sample-weighted NHANES descriptive and model-sensitivity run, reported separately from the hospital-triage primary model.
- Exclusion/adjudication sensitivity excluding patients with competing liver-disease signals when reliable structured fields are available.
- Future sensitivity using alternate endpoint hierarchies, such as biopsy-only, elastography-only, ELF-supported, or physician-adjudicated labels.

### Comparator / Baseline

The primary comparators are FIB-4, APRI, NAFLD Fibrosis Score, ElasticNet logistic regression, screen-all, and screen-none. Secondary comparators in a full-scale EHR study include liver-enzyme-based screening, liver-focused ML models that do not explicitly incorporate CKM interactions, clinician-directed referral workflows, and imaging referral strategies where pre-model clinician decisions are available.

---

## 6. Clinical Integration and Expected Outputs

### Expected Output Format

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

The default clinician view is a simplified explanation panel grouped into clinically meaningful domains. The audit view includes a full SHAP waterfall plot showing how the patient's predicted risk moves from cohort baseline risk to the final calibrated score.

### Clinical Integration

The model functions as an upstream EHR-integrated pre-screening and triage tool. Inference is triggered when updated structured data become available, such as annual metabolic labs, medication updates, or clinic review. High-risk patients prompt non-invasive fibrosis evaluation, hepatology review, trial pre-screening, or intensified cardiometabolic management. Intermediate-risk patients prompt completion of missing labs, repeat scoring, ELF/FibroScan consideration, or clinician review depending on local capacity.

The system is passive clinical decision support rather than autonomous diagnosis. Clinicians retain final decision-making authority and can override recommendations.

### Commercialization and Deployment Strategy

The platform could be deployed as a B2B clinical decision-support product for hospitals, healthcare systems, accountable care organizations, and specialty clinics through subscription-based licensing. It could also be integrated as a modular risk-stratification component within existing EHR or digital-health platforms through licensing, royalties, milestone-based partnerships, or white-label deployment. Its value aligns with Dyania-style care-gap closure: earlier patient identification, more efficient trial recruitment, and more systematic use of existing clinical data.

---

## 7. Ethical Considerations and Data Privacy

### IRB / Ethics Review

NHANES is publicly available and de-identified. A real hospital EHR validation would require IRB review or exemption determination, data-use agreements, and site governance approval. Retrospective de-identified or pseudonymized EHR development may qualify for expedited review or waiver of informed consent when risk is minimal and individual consent is impracticable. Prospective deployment or clinician-facing alerts require additional oversight because model outputs can influence care pathways.

### Data Privacy

All production development and inference should occur inside the healthcare system environment or approved secure infrastructure. PHI should not be sent to external model APIs. Data extraction should follow HIPAA, GDPR where applicable, institutional privacy policies, minimum necessary access, role-based permissions, audit logging, encryption, retention limits, and secure authentication.

The study should apply:

- Access controls: centrally managed credentials and role-based permissions.
- Integrity controls: procedures ensuring ePHI is not improperly altered, corrupted, or destroyed.
- Audit controls: logging, monitoring, and review of ePHI access and usage.
- Network security protections: encryption, secure authentication, firewalls, and safeguards for PHI at rest and in transit.

Direct identifiers such as names, addresses, phone numbers, and medical record numbers are not model inputs. Notes are used only through approved chart-abstraction pipelines or de-identified review.

### Algorithmic Fairness

Potential retrospective bias sources include selection bias from patients more likely to undergo advanced liver evaluation, information bias from heterogeneous EHR documentation, missing-data bias, misclassification from non-biopsy surrogate labels, and confounding from overlapping metabolic, cardiovascular, and kidney risk factors.

Mitigation strategies include predefined inclusion/exclusion criteria, standardized endpoint hierarchy, structured preprocessing, missingness indicators, subgroup analyses, calibration assessment, sensitivity analyses, external validation, and post-deployment monitoring.

Future deployment must also monitor non-participation bias, loss to follow-up, temporal performance drift, and unequal model performance across demographic or clinical subgroups. If subgroup calibration or false-negative rates diverge materially, thresholds can be recalibrated, missingness handling reviewed, and site-specific implementation constrained until safety is demonstrated.

### Clinical Transparency

The system is clinical decision support, not autonomous diagnosis. It shows risk score, tier, recommended next step, model input summary, missing-data flags, grouped factor contributions, and confidence/caveat indicators when inputs are insufficient. Clinicians can review the basis of the recommendation, override it, and document why.

Consistent with FDA Clinical Decision Support principles, the output should allow healthcare professionals to independently assess the basis of the recommendation rather than rely primarily on automation. A full SHAP waterfall plot remains available for audit, governance, and advanced clinical review.

---

## 8. Limitations and Failure Modes

- NHANES supports proof-of-concept derivation but is cross-sectional and cannot prove longitudinal progression.
- VCTE liver stiffness is a non-invasive fibrosis proxy and does not fully replace biopsy-confirmed MASH.
- Ground-truth fibrosis confirmation is not universally available in EHR populations, introducing potential label noise.
- Acute hepatitis, alcohol-related liver disease, viral hepatitis, pregnancy, rare liver diseases, inflammatory conditions, transplant history, and decompensated disease can make routine risk scores misleading.
- Sparse labs, inconsistent units, site-specific coding practices, and heterogeneous documentation can degrade performance.
- Healthcare utilization patterns can bias who receives confirmatory testing.
- Intermediate-risk outputs require clinician judgment and local testing capacity rather than automatic referral for every patient.
- XGBoost probabilities require calibration before they should be presented as patient-facing risk percentages.
- SHAP explanations support transparency but are not causal claims.
- Full-scale deployment requires external validation, prospective monitoring, fairness auditing, and site-specific recalibration.
