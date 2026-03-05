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
6. Governance Recommendations


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

## 6. Governance Recommendations





## Team Coordination Update

At this stage of the project, the team is organizing the remaining work as follows:
- The Data Scientist and the Governance Officer will finalize their sections by Thursday.
- The Data Engineer and the Product Lead have started organizing the structure of the presentation and preparing the content for the final video submission.
