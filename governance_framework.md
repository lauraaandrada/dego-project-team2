# Governance Framework – Credit Scoring System

## 1. Regulatory Context
This credit scoring system qualifies as a high-risk AI system under the EU AI Act.
It is also subject to GDPR due to the processing of personal data.

## 2. Identification of Personal Data (PII)
The dataset contains the following personal data:

### Direct Identifiers
- full_name
- email
- ssn
- ip_address

### Indirect Identifiers (Quasi-identifiers)
- date_of_birth
- zip_code
- gender

These fields can directly or indirectly identify individuals and therefore fall under GDPR Article 4(1) definition of personal data.

## 3. Pseudonymization & Data Minimization Strategy

To reduce privacy risks in the credit scoring system, the following measures will be implemented:

- full_name will be replaced with a unique internal user_id.
- ssn will be removed after identity verification.
- ip_address will be truncated or hashed before storage.
- date_of_birth will be converted into age bands (e.g., 18–25, 26–35).

These measures align with:
- GDPR Article 5(1)(c) – Data Minimization
- GDPR Article 25 – Data Protection by Design

## 4. Data Governance Assessment
We will assess:
- Missing values
- Data consistency
- Duplicates
- Outliers
- Data validity

## 5. Bias & Fairness Risk
We will evaluate potential bias related to:
- Gender
- Age
- Income
- Region

We will assess disparate impact across protected groups.

## 6. AI Risk Management
We will:
- Identify risks
- Assess impact
- Define mitigation strategies
- Propose monitoring mechanisms

## 7. Accountability & Documentation
- Git will serve as audit trail
- Clear role responsibilities
- Transparent documentation of decisions