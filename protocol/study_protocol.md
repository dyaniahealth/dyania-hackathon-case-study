# Study Protocol

> **Instructions:** This is the main deliverable. Replace every `> *Fill in:*` block with your team's content. Do not leave any section empty in your final submission.

---

## 1. Study Title and Objectives

**Title:**
> *Fill in: A concise, descriptive title for your proposed study.*

**Primary Objective:**
> *Fill in: One sentence. What is the main clinical question your system answers?*

**Secondary Objectives:**
> *Fill in: 2–4 additional goals (e.g., CKM subgroup characterisation, trial recruitment precision, time-to-referral reduction).*

---

## 2. Target Population

### Inclusion Criteria
> *Fill in: Who qualifies for pre-screening? Consider age, diagnosis codes (ICD-10), lab availability, minimum follow-up period.*

### Exclusion Criteria
> *Fill in: Who should be excluded? Consider confounders (alcohol-related liver disease, viral hepatitis, prior transplant, etc.).*

### Cohort Size Estimate
> *Fill in: How large does the target cohort need to be for your proposed validation? Justify briefly.*

---

## 3. Endpoints

### Primary Endpoint
> *Fill in: What is the main outcome your model predicts? How is it defined and measured? What performance threshold makes it clinically useful?*

### Secondary Endpoints
> *Fill in: List 2–4 secondary outcomes with their measurement approach.*

| Endpoint | Measurement | Timeframe |
|---|---|---|
| | | |
| | | |

---

## 4. Proposed Data Sources

> *Fill in: List each data source your system would ingest. For each, describe: what data type it provides, how it would be accessed in a real deployment, and what specific variables are relevant.*

| Source | Data Type | Access Pathway | Key Variables |
|---|---|---|---|
| | | | |
| | | | |

**Ground Truth Definition:**
> *Fill in: How do you define a confirmed MASH case? What is your hierarchy (biopsy, FibroScan, clinical surrogate)? This is critical — be specific.*

---

## 5. Statistical Analysis Plan

### Sample Size
> *Fill in: How many cases and controls do you need? Justify using event-per-variable (EPV) or a power calculation.*

### Train / Validation / Test Split
> *Fill in: How will you partition data? How do you ensure no data leakage? How do you handle class imbalance?*

### Evaluation Metrics
> *Fill in: Which metrics will you use to evaluate your model? Why are they appropriate for this problem (consider prevalence, clinical cost of false negatives vs. false positives)?*

### Subgroup Analyses
> *Fill in: Which patient subgroups will you evaluate separately? Why?*

### Comparator / Baseline
> *Fill in: What is your model compared against? (e.g., FIB-4, NFS, physician gestalt)*

---

## 6. Ethical Considerations and Data Privacy

### IRB / Ethics Review
> *Fill in: What ethical review would be required for this study? What exemptions might apply for retrospective de-identified data?*

### Data Privacy
> *Fill in: How do you ensure HIPAA compliance? How is PHI handled during model training and inference?*

### Algorithmic Fairness
> *Fill in: How will you evaluate and mitigate bias across demographic subgroups?*

### Clinical Transparency
> *Fill in: How are model outputs presented to clinicians? What safeguards ensure the model is decision-support rather than decision-replacement?*
