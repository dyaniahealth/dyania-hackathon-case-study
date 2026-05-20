# ML Approach

> **Instructions:** Describe your machine learning methodology. Be specific — vague answers score low. Every choice should have a justification tied to the clinical context.

---

## 1. Problem Formulation

> *Fill in: How did you frame the ML task? (e.g., binary classification, multi-label classification, risk scoring, survival analysis). Why is this framing appropriate for MASH-CKM pre-screening?*

---

## 2. Chosen Model(s)

> *Fill in: What model(s) did you select? List your primary model and any ensemble or secondary models.*

| Model | Role | Justification |
|---|---|---|
| | Primary | |
| | Baseline / comparator | |

> *Fill in: Why is your chosen model appropriate for this data type and clinical use case? Address: handling of missing values, interpretability requirements, performance on tabular data, scalability to EHR volumes.*

---

## 3. Feature Engineering

> *Fill in: What raw inputs does your model use? How do you transform them into model-ready features?*

### Input Variables
> *List the raw clinical variables your model ingests (e.g., labs, vitals, diagnosis codes, medication exposures, imaging findings).*

### Engineered Features
> *List any derived or composite features (e.g., FIB-4 index, NAFLD Fibrosis Score, CKD stage from eGFR + creatinine, metabolic syndrome flag). Explain why each adds signal.*

| Feature | Derivation | Clinical Rationale |
|---|---|---|
| | | |
| | | |

### Handling Missing Data
> *Fill in: What is your strategy for missing values? (e.g., median imputation, MICE, model-native handling). How do you flag informative missingness?*

---

## 4. Validation Strategy

> *Fill in: How do you ensure your model generalises and does not overfit to your training cohort?*

- **Train / Validation / Test split:**
- **Cross-validation approach:**
- **Temporal validation** (if applicable — training on earlier dates, testing on later):
- **External validation** (if applicable — held-out site or dataset):

---

## 5. Expected Model Outputs

> *Fill in: What does a single model inference return? (e.g., probability score per label, risk tier, ranked feature contributions). How is this output consumed by a clinician or downstream system?*

**Output format:**
> *e.g., "Three probability scores (liver risk, cardiac risk, renal risk) + composite CKM-MASH tier (Low / Moderate / High) + top-3 contributing features via SHAP"*

---

## 6. Clinical Integration

> *Fill in: Where does this model sit in the clinical workflow? (e.g., passive EHR flag, referral decision support, trial recruitment filter). What triggers a model inference? What action does a High-risk flag prompt?*

---

## 7. Limitations and Failure Modes

> *Fill in: Where does your model break down? What patient profiles or data quality scenarios lead to unreliable outputs? How should clinicians be informed of these?*
