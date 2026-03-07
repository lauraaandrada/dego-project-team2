# DEGO Project – Team 2

## Project Description
**Credit application data quality and bias analysis for the Data Ecosystems and Governance in Organizations course**

## Team Members

| Role | Name | Student ID |
|------|------|------------|
| Data Engineer | Sivert Ivarhus | 70142 |
| Data Scientist | Behnia Ghadiani | 71819 |
| Governance Officer | Pauline Kragset Pleym | 74537 |
| Product Lead | Laura Andrada | 52013 |

## Table of Contents

1. Repository Structure
2. Executive Summary
3. Dataset
4. Data Quality Findings
5. Bias Analysis
6. Privacy & Governance
7. Governance Recommendations


## 1. Repository Structure

```
dego-project-team2/
├── data/
│   └── data.json
├── notebooks/
│   ├── 01-Data-Quality.ipynb
│   ├── 02-bias-analysis.ipynb
│   └── 03-privacy-demo.ipynb
├── presentation/
├── reports/
├── src/
│   └── __init__.py
├── .gitignore
└── README.md
```

## 2. Executive Summary

## 3. Dataset

**File:** `data/data.json`  
**Records:** 502 credit applications  
**Format:** Nested JSON structure

### Schema Overview

The dataset includes the following main components:

- **Applicant Information:** `name`, `email`, `SSN`, `IP`, `gender`, `date_of_birth`, `zip_code`
- **Financial Information:** `annual_income`, `credit_history_months`, `debt_to_income`, `savings_balance`
- **Spending Behavior:** array describing transaction patterns
- **Decision Information:** `loan_approved`, `interest_rate`, `loan_amount`, `rejection_reason`

## 4. Data Quality Findings

**Notebook:** `01-Data-Quality.ipynb`

The data quality assessment was conducted using key data governance dimensions, including completeness, consistency, accuracy, and timeliness. These dimensions help evaluate whether the dataset is reliable and suitable for analytical and decision-making purposes.

The analysis focused on identifying structural inconsistencies, missing values, data type issues, and implausible financial values that could affect the reliability of the dataset.

### Completeness and Structural Integrity

The analysis started with an assessment of the structural integrity of the dataset. Duplicate application IDs were identified and removed to preserve primary key consistency. After deduplication, the `notes` field no longer had analytical relevance and was therefore removed.

A structural redundancy was also identified between the variables `annual_income` and `annual_salary`. Instead of treating this issue as missingness, the two variables were unified to enforce schema consistency.

### Data Type and Formatting

Several data type inconsistencies were identified. Date values were stored in multiple formats, so controlled multi-format parsing was applied and all dates were standardized before deriving the age variable used in the analysis.

Numeric attributes were validated to ensure they were stored using the correct data types.

### Categorical Encoding Consistency

Categorical variables presented inconsistent encodings. For example, the `gender` attribute contained multiple representations such as “Male” and “M”. These values were standardized to prevent category fragmentation during analysis.

### Plausibility Checks

Plausibility checks were performed on key financial variables. Debt-to-income ratios and interest rates were found to be within realistic ranges. However, one negative savings value was identified and corrected through controlled nullification.

### Timeliness

Timeliness could not be robustly evaluated due to the limited availability of timestamp data. The `processing_timestamp` field appeared only in a minority of records and lacked a reference event required to calculate processing delays.

### Accuracy

Direct verification of accuracy was not possible due to the absence of external ground truth. Instead, accuracy was assessed indirectly through plausibility checks and structural consistency across related variables.

### Data Quality Summary

Overall, the dataset presents acceptable structural integrity after the cleaning process. Duplicate records were removed, data types were standardized, and categorical variables were normalized to prevent inconsistencies during analysis. Financial variables passed plausibility checks with only minor corrections required.

However, some governance limitations remain. Timeliness could not be properly evaluated due to limited timestamp information, and accuracy could only be assessed indirectly through plausibility and structural checks due to the absence of external ground truth.

These findings indicate that while the dataset is suitable for analytical exploration, additional governance controls would be required before using it in production decision systems.

## 5. Bias Analysis

**Notebook:** `02-bias-analysis.ipynb`

#### Selection Bias 

The gender distribution in the dataset is nearly balanced: **50.4% female, 49.6% male**. A chi-square test confirms no statistically significant deviation from 50/50 (χ² = 0.03, p = 0.86), meaning no evidence of gender-based selection bias in who is represented.

#### Outcome Bias — Significant

Despite balanced representation, approval outcomes differ substantially:

| Group  | Approval Rate | DI Ratio (vs Male) | 80% Rule |
|--------|--------------|---------------------|----------|
| Male   | 66%          | 1.00 (reference)    | ✅ Pass  |
| Female | 51%          | **0.77**            | ❌ Fail  |

- **Disparate Impact Ratio: 0.77** — below the 0.80 legal threshold (Four-Fifths Rule)
- **Demographic Parity Difference: 0.15** — approval rates are not independent of gender
- **80% rule threshold: 52.8%** — female approval rate (51%) falls below this

#### Statistical Confirmation

A two-proportion z-test confirms the disparity is not due to random variation:

```
z = −3.483,  p = 0.0005
```

The observed gender gap is **statistically significant** and reflects a systematic pattern in historical lending decisions.

### Age Bias

#### Selection Bias - Significant

Unlike gender, the age distribution deviates significantly from uniform (χ² = 296.46, p < 0.001). Applicants aged 25–44 are overrepresented, while the youngest (18–24) and oldest (65+) cohorts are underrepresented. Findings for these extremes should be interpreted with caution.

#### Approval Rates by Age Group

| Age Group | Approval Rate | DI Ratio (vs 35–44) | 80% Rule |
|-----------|--------------|----------------------|----------|
| 18–24     | 50%          | 0.75                 | ❌ Fail  |
| 25–34     | 45%          | 0.67                 | ❌ Fail  |
| 35–44     | **66%**      | 1.00 (reference)     | ✅ Pass  |
| 45–54     | 64%          | 0.97                 | ✅ Pass  |
| 55–64     | 62%          | 0.94                 | ✅ Pass  |
| 65+       | 54%          | 0.81                 | ✅ Pass  |

- **Demographic Parity Difference across age groups: 0.22** (larger than gender gap of 0.15)
- **Chi-square test:** χ² = 18.14, p = 0.003
- **Cramér's V: 0.19** — small-to-moderate association

Two age groups (18–24 and 25–34) violate the 80% rule. The pattern likely partially reflects legitimate financial factors — younger applicants tend to have shorter credit histories — but this itself creates a proxy risk (see below).

#### Interest Rates & Approved Amounts

Interest rates are broadly stable across groups, but the 65+ cohort receives noticeably higher rates (+0.81 pp above average). The 18–24 group receives substantially lower approved amounts on average (€34,600 vs €47,846 overall average).

### Proxy Discrimination

Proxy discrimination occurs when non-protected variables indirectly encode protected characteristics. A logistic regression identified `credit_history_months` and `annual_income` as the key drivers of loan approval. These were then tested as proxies.


#### Age Proxies

| Proxy Variable              | Correlation with Age | Significance   | Verdict           |
|-----------------------------|----------------------|----------------|-------------------|
| `credit_history_months`     | r = **0.652**        | p < 0.001      |  Indirect proxy |
| `annual_income`             | r = 0.394            | p < 0.001      | Moderate risk  |

`credit_history_months` has a strong correlation with age (r = 0.652, p < 0.001) **and** significantly increases approval probability (logistic regression, p = 0.026). Using it as a feature may inadvertently penalise younger applicants for their age.

#### Gender Proxies

Neither `credit_history_months` (p = 0.555) nor `annual_income` (p = 0.326) differ significantly between genders — these variables do **not** act as gender proxies.

#### ZIP Code as Proxy

| Association        | Test           | Statistic        | Effect Size     | Verdict               |
|--------------------|----------------|------------------|-----------------|-----------------------|
| ZIP ↔ Approval     | Chi-square     | χ² = 8.15, p = 0.017 | V = 0.13 (weak) | Minor concern        |
| ZIP ↔ Gender       | Chi-square     | χ² = 324.67, p < 0.001 | **V = 0.81 (strong)** |  Strong proxy  |
| ZIP ↔ Income       | ANOVA          | F = 0.76, p = 0.467 | η = 0.06 (negligible) |  No proxy    |
| ZIP ↔ Age          | ANOVA          | F = 0.41, p = 0.663 | η = 0.04 (negligible) |  No proxy    |

**ZIP code is a strong proxy for gender (V = 0.81)** — geographic location carries substantial demographic information. Although its direct effect on approval is weak (V = 0.13), including ZIP code in a model could reintroduce gender-related patterns indirectly.

### Age × Gender Interactions

A logistic regression model including age group, gender, and their interaction showed **no statistically significant interaction terms** (all p > 0.05). This indicates that the gender gap in loan approvals is consistent across all age groups — male applicants have higher approval rates regardless of age bracket

Descriptively, the largest gender gaps appear in the youngest cohorts (18–34), but the regression confirms these are not significantly amplified — the overall gender disadvantage simply compounds with age-related disadvantage for young female applicants.

## 6. Privacy & Governance 

**Notebook:** `03-privacy-demo.ipynb`


### PII Identification & Classification

The raw dataset contains **21 attributes**, of which **9 were identified as PII** under GDPR Art. 4(1). These fall into four categories:

| Field                 | Category                       | Risk      | Action Taken     | GDPR Article |
|-----------------------|--------------------------------|-----------|------------------|--------------|
| `full_name`           | Direct identifier              | HIGH      | Removed          | Art. 4(1)    |
| `email`               | Direct identifier              | HIGH      | Pseudonymised    | Art. 4(5)    |
| `ssn`                 | Highly sensitive identifier    | CRITICAL  | Pseudonymised    | Art. 9       |
| `ip_address`          | Online identifier              | HIGH      | Removed          | Art. 4(1)    |
| `date_of_birth`       | Quasi-identifier               | MEDIUM    | Retained         | Art. 4(1)    |
| `zip_code`            | Quasi-identifier               | MEDIUM    | Retained         | Art. 4(1)    |
| `gender`              | Protected attribute            | MEDIUM    | Retained         | Art. 9       |
| `spending_behavior`   | Sensitive behavioural data     | MEDIUM    | Retained         | Art. 4(1)    |
| `notes`               | Unstructured PII               | LOW       | Reviewed         | Art. 4(1)    |

> **4 out of 9 PII fields were removed or pseudonymised** before the analytical dataset was produced.

Quasi-identifiers such as `date_of_birth` and `zip_code` do not uniquely identify individuals on their own, but research shows that combinations of such attributes can significantly increase re-identification risk when linked with external datasets.


### Pseudonymisation

To demonstrate GDPR-compliant handling, email addresses were pseudonymised using **SHA-256 hashing**, and the original column was then removed from the analytical dataset.

```
Original email:   jerry.smith17@hotmail.com
SHA-256 hash:     116648a7761525746032d0ab323ceb01f50d11f793516...
```

This aligns with:
- **GDPR Art. 4(5)** — definition of pseudonymisation
- **GDPR Art. 5(1)(c)** — data minimisation: only the minimum necessary data is retained

In a production system, the mapping between hash and original identity should be stored in a separate, access-controlled identity store — completely isolated from the analytical pipeline.


### GDPR Mapping

| GDPR Article         | Principle                  | Application in NovaCred                                                  | Status      |
|----------------------|----------------------------|---------------------------------------------------------------------------|-------------|
| Art. 4(1)            | Personal Data              | Name, email, SSN, IP, ZIP, DOB qualify as personal data                  |  Identified |
| Art. 4(5)            | Pseudonymisation           | Email hashed via SHA-256; original removed from analytical dataset        |  Applied    |
| Art. 5(1)(c)         | Data Minimisation          | Direct identifiers removed post-analysis                                 |  Applied    |
| Art. 5(1)(e)         | Storage Limitation         | No retention policy currently defined                                    |  Gap        |
| Art. 5(1)(f)         | Integrity & Confidentiality| Sensitive fields require encryption and access controls                  |  Partial   |
| Art. 6               | Lawful Basis               | Must rely on contractual necessity for processing loan applications       |  Not logged |
| Art. 22              | Automated Decisions        | Applicants have the right to human review of automated decisions          |  Gap        |
| Art. 32              | Security of Processing     | SSN and sensitive fields require hashing, encryption & access control    |  Partial   |

**Three critical compliance gaps identified:** no consent/lawful-basis logging, no data retention policy, and no human review path for automated decisions.


### EU AI Act Classification

The NovaCred credit scoring system is classified as a **HIGH-RISK AI system** under the EU AI Act.

| AI Act Aspect          | Assessment                                                                 |
|------------------------|----------------------------------------------------------------------------|
| **Risk Category**      | HIGH RISK                                                                  |
| **Legal Reference**    | EU AI Act — Annex III, Point 5(b)                                          |
| **Use Case**           | Credit scoring / creditworthiness evaluation                               |
| **Impact**             | Automated decisions may affect individuals' access to financial services   |
| **Key Requirements**   | Risk management, data governance, transparency, logging, human oversight   |

High-risk AI systems must comply with strict requirements before deployment including a **conformity assessment**, technical documentation, and registration in the EU AI Act national database. They must also include mechanisms for human oversight and ongoing bias monitoring.

---
## 6. Governance Recommendations


*NovaCred Credit Application Governance Analysis — DEGO 2606, Nova SBE, Spring Semester 2026*
*Team: Sivert Ivarhus · Behnia Ghadiani · Pauline Kragset Pleym · Laura Andrada*
