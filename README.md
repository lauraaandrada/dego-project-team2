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

## Project Presentation Video

Watch the project presentation video here:  
[DEGO Project Presentation](https://youtu.be/WzzdKxyw6mA)

## How to Run

### Requirements
- Python 3.10+
- Jupyter Notebook

Install the required libraries:

```
pip install pandas numpy matplotlib seaborn scikit-learn
```
### Notebook Execution Order

The analysis is organized in three notebooks:

**1. 01-Data-Quality.ipynb**  
   Performs data cleaning and prepares the dataset for analysis.

**2. 02-bias-analysis.ipynb**  
   Runs fairness metrics, statistical tests, and bias detection.

**3. 03-privacy-demo.ipynb**  
   Demonstrates PII identification, pseudonymisation, and GDPR / EU AI Act compliance.

Run the notebooks in this order to reproduce the analysis.

## Table of Contents

1. Repository Structure
2. Executive Summary
3. Dataset
4. Data Quality Findings
5. Bias Analysis
6. Privacy & Governance
7. Governance Recommendations
8. Conclusion


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

This project evaluates the NovaCred credit application dataset from a **data governance perspective**, focusing on four key dimensions: **data quality, algorithmic bias, privacy compliance, and regulatory governance**. The objective is to assess whether the dataset and decision pipeline are suitable for responsible use in automated credit scoring systems.

First, a **data quality assessment** was conducted across five governance dimensions: completeness, uniqueness, consistency, accuracy, and validity. After systematic cleaning, duplicate records were removed, inconsistent formats were standardized, and structural issues were corrected. As a result, overall data quality improved from **93.7% to 99.3%**, producing a reliable analytical dataset of **500 records and 15 variables**.

Second, a **bias analysis** identified statistically significant disparities in loan approval outcomes. Female applicants have a lower approval rate than male applicants (51 percent vs 66 percent), producing a **disparate impact ratio of 0.77**, which falls below the commonly used 80 percent fairness threshold. Younger applicants, particularly those aged **18 to 34**, also show lower approval rates. In addition, variables such as **credit history length and ZIP code** present potential proxy discrimination risks.

Third, the project evaluated **privacy and regulatory compliance under GDPR**. Nine attributes were identified as personal data, and several direct identifiers were removed or pseudonymised to reduce re-identification risk. However, important governance gaps remain, including the absence of a **data retention policy**, missing **lawful basis tracking**, and no **human review mechanism** for automated decisions.

Finally, the system was assessed under the **EU AI Act**, which classifies automated credit scoring as a **high-risk AI system**. Such systems require strict governance controls, including transparency, audit logging, bias monitoring, human oversight, and a formal conformity assessment before deployment.

Overall, while the dataset is suitable for analytical modeling after cleaning and privacy safeguards, **fairness risks and regulatory compliance gaps must be addressed** before the system could be responsibly deployed.

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

The data quality assessment was conducted using key data governance dimensions, including completeness, uniqueness, consistency, accuracy, and validity. Timeliness was considered but could not be evaluated due to limited timestamp information.

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

| Dimension     | Before Cleaning | After Cleaning | Key Actions |
|---------------|----------------|---------------|-------------|
| Completeness  | 86.2% | 97.1% | Standardized missing tokens (`NA`, `unknown`), addressed structural missingness in decision fields |
| Uniqueness    | 99.2% | 99.6% | Removed duplicate application IDs (502 → 500 records) |
| Consistency   | 83.5% | 100% | Converted `annual_income` strings to numeric, standardized date and gender formats |
| Accuracy      | 100% | 100% | Resolved SSN conflicts and validated decision logic |
| Validity      | 99.8% | 99.8% | Corrected one negative savings value and invalid email formats |

After cleaning, the dataset contains:

- 500 records  
- 15 variables  
- Personally identifiable fields removed or nullified when conflicts occurred  

The overall average data quality score improved from **93.7% to 99.3%** after the cleaning process.

Overall, the dataset demonstrates strong structural integrity after the cleaning process. Duplicate records were removed, data types were standardized, and categorical encodings were normalized to prevent inconsistencies during analysis. Financial variables passed plausibility checks with only minor corrections required.

However, some governance limitations remain. Timeliness could not be properly evaluated due to limited timestamp information, and accuracy could only be assessed indirectly because no external ground truth is available.


## 5. Bias Analysis

**Notebook:** `02-bias-analysis.ipynb`

The bias analysis evaluates whether loan approval outcomes differ systematically across demographic groups. The assessment focuses on gender and age disparities, as well as the potential presence of proxy discrimination through non-protected variables.

### Gender Bias

#### Selection Bias

The gender distribution in the dataset is balanced, with 50.4% female applicants and 49.6% male applicants. A chi-square test shows no statistically significant deviation from an equal distribution (chi-square = 0.03, p = 0.86). This indicates that there is no evidence of gender-based selection bias in the applicant pool.

#### Outcome Bias

Despite balanced representation, approval outcomes differ substantially between genders.

| Group  | Approval Rate | Disparate Impact Ratio (vs Male) | 80 Percent Rule |
|--------|--------------|----------------------------------|----------------|
| Male   | 66 percent   | 1.00 (reference group)           | Within threshold |
| Female | 51 percent   | 0.77                             | Below threshold |

The disparate impact ratio is 0.77, which falls below the commonly used 0.80 threshold of the Four-Fifths Rule. This indicates potential adverse impact against female applicants.

Additional fairness metrics confirm this pattern:

- Demographic parity difference: 0.15  
- 80 percent rule threshold: 52.8%

The female approval rate of 51% falls below the threshold, indicating that approval outcomes are not independent of gender.

#### Statistical Confirmation

A two-proportion z-test confirms that the observed disparity is statistically significant.

- z statistic: -3.483  
- p-value: 0.0005

This result indicates that the gender approval gap is unlikely to be explained by random variation and instead reflects a systematic pattern in historical lending outcomes.

### Age Bias

#### Selection Bias

The distribution of applicants across age groups is not uniform. A chi-square test shows significant deviation from an even distribution (chi-square = 296.46, p < 0.001). Applicants aged 25 to 44 are overrepresented, while the youngest (18 to 24) and oldest (65 and above) cohorts are underrepresented.

Because of this imbalance, findings related to these extreme age groups should be interpreted cautiously.

#### Approval Rates by Age Group

| Age Group | Approval Rate | Disparate Impact Ratio (vs 35–44) | 80 Percent Rule |
|-----------|--------------|------------------------------------|----------------|
| 18–24 | 50 percent | 0.75 | Below threshold |
| 25–34 | 45 percent | 0.67 | Below threshold |
| 35–44 | 66 percent | 1.00 (reference group) | Within threshold |
| 45–54 | 64 percent | 0.97 | Within threshold |
| 55–64 | 62 percent | 0.94 | Within threshold |
| 65 and above | 54 percent | 0.81 | Within threshold |

Two age groups (18–24 and 25–34) fall below the 80% rule threshold, indicating potential adverse impact for younger applicants.

Additional fairness metrics show:

- Demographic parity difference across age groups: 0.22  
- Chi-square statistic: 18.14  
- p-value: 0.003  
- Cramer's V: 0.19

These results suggest a small to moderate association between age group and approval outcomes.

Part of this pattern may reflect legitimate financial characteristics, as younger applicants typically have shorter credit histories. However, this relationship introduces a potential proxy discrimination risk.

#### Interest Rates and Approved Loan Amounts

Interest rates remain relatively stable across most age groups. However, applicants aged 65 and above receive slightly higher interest rates on average, approximately 0.81 percentage points above the overall mean.

Applicants aged 18 to 24 receive substantially lower approved loan amounts on average, approximately 34,600 euros compared to the dataset average of 47,846 euros.

### Proxy Discrimination

Proxy discrimination occurs when variables that are not protected attributes indirectly encode demographic characteristics.

A logistic regression model identified `credit_history_months` and `annual_income` as the strongest predictors of loan approval. These variables were therefore examined as potential proxies.

#### Age Proxies

| Proxy Variable | Correlation with Age | Significance | Interpretation |
|---------------|----------------------|-------------|---------------|
| credit_history_months | 0.652 | p < 0.001 | Strong proxy risk |
| annual_income | 0.394 | p < 0.001 | Moderate proxy risk |

The variable `credit_history_months` shows a strong correlation with age and significantly increases approval probability in the logistic regression model (p = 0.026). Because credit history length naturally increases with age, the use of this feature may indirectly disadvantage younger applicants.

#### Gender Proxies

Statistical tests show that neither `credit_history_months` nor `annual_income` differ significantly across genders (p-values of 0.555 and 0.326 respectively). These variables therefore do not appear to function as gender proxies.

#### ZIP Code as a Proxy

| Relationship | Test | Statistic | Effect Size | Interpretation |
|--------------|------|-----------|-------------|---------------|
| ZIP code and approval | Chi-square | 8.15 (p = 0.017) | Cramer's V = 0.13 | Weak association |
| ZIP code and gender | Chi-square | 324.67 (p < 0.001) | Cramer's V = 0.81 | Strong association |
| ZIP code and income | ANOVA | F = 0.76 (p = 0.467) | Eta = 0.06 | No meaningful relationship |
| ZIP code and age | ANOVA | F = 0.41 (p = 0.663) | Eta = 0.04 | No meaningful relationship |

ZIP code shows a strong association with gender, indicating that geographic location carries significant demographic information. Although its direct relationship with approval outcomes is weak, including ZIP code as a feature in a model could indirectly reintroduce gender-related patterns.

### Age and Gender Interaction Effects

A logistic regression model including age group, gender, and their interaction terms shows no statistically significant interaction effects. All interaction coefficients have p-values greater than 0.05.

This indicates that the gender approval gap remains consistent across all age groups. Male applicants have higher approval rates regardless of age category.

Descriptive statistics suggest that the largest gender differences appear among younger applicants aged 18 to 34. However, the regression results confirm that this pattern is not statistically amplified by age. Instead, the gender disadvantage and the age-related disadvantage operate independently and may compound for younger female applicants.

### Key Bias Findings

| Area | Finding | Risk Level |
|-----|--------|-----------|
| Gender | Female approval rate significantly lower than male approval rate | High |
| Age | Younger applicants (18–34) have lower approval rates | Moderate |
| Proxy Variables | Credit history strongly correlated with age | Moderate |
| Geographic Proxy | ZIP code strongly associated with gender | Moderate |
| Interaction Effects | No significant age–gender interaction detected | Low |

These results indicate that the dataset contains measurable demographic disparities and potential proxy risks that should be carefully monitored before using the model in production decision systems.

## 6. Privacy & Governance

**Notebook:** `03-privacy-demo.ipynb`

This section evaluates the dataset from a **privacy, regulatory, and governance perspective**, focusing on GDPR compliance and the regulatory obligations introduced by the **EU AI Act** for automated credit decision systems.

The analysis identifies personal data fields, applies privacy-preserving transformations, and evaluates whether the system meets the governance requirements expected for high-risk AI systems.

### PII Identification and Classification

The raw dataset contains **21 attributes**, of which **9 were identified as personal data under GDPR Article 4(1)**. These include direct identifiers, quasi-identifiers, and sensitive attributes.

| Field | Category | Risk Level | Action Taken | GDPR Reference |
|------|---------|-----------|-------------|---------------|
| `full_name` | Direct identifier | High | Removed | Art. 4(1) |
| `email` | Direct identifier | High | Pseudonymised | Art. 4(5) |
| `ssn` | Highly sensitive identifier | Critical | Pseudonymised | Art. 9 |
| `ip_address` | Online identifier | High | Removed | Art. 4(1) |
| `date_of_birth` | Quasi-identifier | Medium | Retained | Art. 4(1) |
| `zip_code` | Quasi-identifier | Medium | Retained | Art. 4(1) |
| `gender` | Protected attribute | Medium | Retained | Art. 9 |
| `spending_behavior` | Behavioural data | Medium | Retained | Art. 4(1) |
| `notes` | Unstructured personal data | Low | Reviewed | Art. 4(1) |

In total, **four of the nine identified PII fields were removed or pseudonymised** before producing the analytical dataset.

Quasi-identifiers such as `date_of_birth` and `zip_code` do not uniquely identify individuals on their own. However, combinations of such attributes may enable re-identification when linked with external datasets, which creates residual privacy risk.

### Pseudonymisation

To demonstrate GDPR-compliant handling of personal data, email addresses were pseudonymised using **SHA-256 hashing**, after which the original column was removed from the analytical dataset.

*Example:*
- Original email: jerry.smith17@hotmail.com
- SHA-256 hash: 116648a7761525746032d0ab323ceb01f50d11f793516...

This approach aligns with:
- GDPR Article 4(5) — Definition of pseudonymisation  
- GDPR Article 5(1)(c) — Data minimisation principle

In a production environment, the mapping between hashed values and original identities should be stored in a **separate secure identity store with strict access controls**, isolated from the analytical pipeline.

### GDPR Compliance Mapping

The dataset and processing pipeline were evaluated against key GDPR principles.

| GDPR Article | Principle | Application in NovaCred | Status |
|--------------|-----------|-------------------------|-------|
| Art. 4(1) | Personal data definition | Identified personal attributes including name, email, SSN, IP, ZIP and DOB | Implemented |
| Art. 4(5) | Pseudonymisation | Email addresses hashed using SHA-256 | Implemented |
| Art. 5(1)(c) | Data minimisation | Direct identifiers removed before modelling | Implemented |
| Art. 5(1)(e) | Storage limitation | No formal retention policy currently defined | Gap |
| Art. 5(1)(f) | Integrity and confidentiality | Sensitive attributes require encryption and access control | Partial |
| Art. 6 | Lawful basis for processing | Contractual necessity assumed but not explicitly logged | Gap |
| Art. 22 | Automated decision rights | No mechanism for human review currently implemented | Gap |
| Art. 32 | Security of processing | Sensitive attributes require hashing, encryption and access control | Partial |

Three major compliance gaps were identified:

- Lack of **lawful basis and consent logging**
- Absence of a **data retention policy**
- No **human review pathway for automated decisions**

These issues would prevent the system from being deployed in a fully GDPR-compliant production environment.

### EU AI Act Classification

The NovaCred credit scoring system qualifies as a **high-risk AI system** under the EU AI Act.

| Assessment Aspect | Evaluation |
|------------------|-----------|
| Risk Category | High Risk |
| Legal Reference | EU AI Act Annex III, Point 5(b) |
| Use Case | Credit scoring and creditworthiness evaluation |
| Impact | Automated decisions affect access to financial services |
| Required Controls | Risk management, data governance, logging, transparency, human oversight |

High-risk AI systems must comply with strict governance requirements before deployment, including:

- formal risk management procedures
- technical documentation and audit trails
- bias monitoring and performance evaluation
- mechanisms for human oversight
- conformity assessment and regulatory registration

## 7. Governance Recommendations

Based on the privacy and bias findings, several governance controls should be implemented before deploying the NovaCred credit scoring system.

### Critical Actions

**7.1. Address gender bias before model deployment**

The bias analysis revealed a statistically significant gender disparity in approval outcomes.  
Model development should incorporate fairness mitigation strategies such as reweighting, resampling, or feature adjustments.

ZIP code should also be reviewed or removed due to its strong association with gender.

**7.2. Implement lawful basis and consent tracking**

Each loan application must record the **legal basis for data processing under GDPR Article 6**.

Applicants should also be informed when automated decision-making is used and must be able to exercise their rights under **GDPR Article 22**, including requesting human review.

### Short-Term Governance Controls

**7.3. Deploy audit logging and human review mechanisms**

Every automated decision should be logged, including input variables, model outputs, and timestamps.  
Rejected applicants must have access to a manual review process.

These measures support transparency and traceability requirements under both **GDPR and the EU AI Act**.

**7.4. Implement data retention and minimisation policies**

A formal retention schedule should be defined for personal data. Sensitive identifiers should be deleted or anonymised once they are no longer necessary for the original processing purpose.

Additional pseudonymisation should also be applied to highly sensitive attributes such as SSN.

### Ongoing Monitoring

**7.5. Establish continuous fairness monitoring**

Fairness metrics such as disparate impact ratios and demographic parity should be evaluated regularly across protected groups.

This monitoring process ensures that model performance and fairness remain within acceptable thresholds over time.

**7.6. Complete EU AI Act conformity assessment**

Before deployment, the system must undergo a **formal conformity assessment** and be registered in the national EU AI Act database.

Technical documentation, risk management procedures, and governance policies must be maintained and periodically reviewed.

Overall, these governance measures ensure that the NovaCred system operates in compliance with **GDPR principles and EU AI Act requirements**, while also mitigating fairness risks identified during the bias analysis.

## 8. Conclusion

This project evaluated the NovaCred credit application dataset from a data governance perspective, focusing on data quality, algorithmic bias, privacy compliance, and regulatory governance.

The data quality assessment identified several structural issues in the raw dataset, including duplicate records, inconsistent formats, and data type mismatches. After systematic cleaning, the overall data quality score improved from 93.7% to 99.3%, and the dataset was reduced from 502 to 500 observations and from 21 to 15 variables, resulting in a more reliable analytical dataset.

The bias analysis revealed statistically significant disparities in loan approval outcomes. Female applicants have a lower approval rate than male applicants, producing a disparate impact ratio of 0.77, which falls below the commonly used 80 percent fairness threshold. Younger applicants, particularly those aged 18 to 34, also experience lower approval rates. These patterns suggest systematic demographic disparities rather than random variation.

From a privacy and governance perspective, several fields were identified as personal data under GDPR, and direct identifiers were removed or pseudonymised to reduce re-identification risk. However, important governance gaps remain, including the absence of a data retention policy, lack of lawful basis tracking, and no mechanism for human review of automated decisions.

Because the system performs automated creditworthiness evaluation, it qualifies as a high-risk AI system under the EU AI Act. As a result, strict governance controls would be required before deployment, including transparency measures, audit logging, human oversight, and continuous fairness monitoring.

Overall, while the cleaned dataset can support analytical modeling, the findings highlight fairness risks and regulatory compliance gaps that must be addressed before the system could be responsibly deployed.

---

*NovaCred Credit Application Governance Analysis - DEGO 2606, Nova SBE, Spring Semester 2026*
*Team: Sivert Ivarhus · Behnia Ghadiani · Pauline Kragset Pleym · Laura Andrada*
