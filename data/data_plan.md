# Data Plan

> **Instructions:** Describe the data your system would use in a real deployment, what you used for this prototype, and how you handle the gap between the two. Be honest about availability assumptions.

---

## 1. Data Sources — Real Deployment

> *Fill in: What data sources would your system ingest in a production clinical environment? For each, describe what it contains, how it would be accessed, and any integration dependencies.*

| Source | Data Type | Access Pathway | Variables Used |
|---|---|---|---|
| | | | |
| | | | |

---

## 2. Data Sources — Prototype / Hackathon

> *Fill in: What data did you actually use to develop and test your prototype? If you used public datasets (MIMIC-IV, NHANES, UK Biobank, etc.), describe which subsets and why they are a reasonable proxy for the target population.*

| Dataset | Size Used | Why Selected | Limitations as Proxy |
|---|---|---|---|
| | | | |
| | | | |

---

## 3. Availability Assumptions

> *Fill in: What assumptions are you making about data availability that may not hold in every clinical site? (e.g., "FibroScan results available for >40% of patients", "ICD-10 coding is consistent across sites"). Flag high-risk assumptions explicitly.*

---

## 4. Preprocessing and Data Quality

### Data Cleaning
> *Fill in: How do you handle duplicate records, out-of-range lab values, unit inconsistencies, free-text fields?*

### Missing Data Strategy
> *Fill in: What is the expected missingness rate for your key features? How do you handle it (imputation method, missingness indicator features, exclusion thresholds)?*

### Temporal Alignment
> *Fill in: How do you handle lab values from different time points? What is your lookback window? How do you select the "index" value for each feature?*

### Label / Ground Truth Construction
> *Fill in: How do you construct your training labels from raw EHR data? What is the prevalence of MASH-positive cases in your dataset? How did you validate label quality?*

---

## 5. Synthetic or Proxy Data (if applicable)

> *Fill in: If you generated synthetic data or used clinical notes from the hackathon package as proxy inputs, describe how. What limitations does this introduce? How would you replace synthetic data in a real study?*

---

## 6. Data Governance and Privacy

> *Fill in: What data use agreements (DUAs) cover your prototype datasets? What steps did you take to ensure no re-identification risk? How would data governance differ in a production deployment?*
