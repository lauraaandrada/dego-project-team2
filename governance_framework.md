# Governance Framework — NovaCred Credit Scoring System

## 1. Regulatory Context
The NovaCred credit scoring system evaluates individuals’ creditworthiness and may influence access to financial services. Because automated decisions can significantly affect individuals, the system qualifies as a **high-risk AI system under the EU AI Act**.

The system also processes personal data and is therefore subject to the **General Data Protection Regulation (GDPR)**. Compliance with both regulatory frameworks requires strong governance practices, including privacy protection, transparency, human oversight, and documentation of automated decisions.

## 2. Identification of Personal Data (PII)
The credit application dataset contains several attributes that qualify as personal data under **GDPR Article 4(1)**.

### Direct Identifiers
These attributes directly identify an individual:
- `full_name`
- `email`
- `ssn`
- `ip_address`
These fields reveal the identity of a specific individual and therefore require strict protection.

### Indirect Identifiers (Quasi-identifiers)
These attributes may not identify individuals on their own but can enable identification when combined with other data:
- `date_of_birth`
- `zip_code`
- `gender`
Quasi-identifiers increase the risk of **re-identification**, especially when datasets are linked with external information.

## 3. Pseudonymization & Data Minimization Strategy
To reduce privacy risks and comply with GDPR principles, the following data protection measures should be implemented:
- `full_name` should be replaced with a unique internal **user_id**.
- `ssn` should be removed after identity verification.
- `ip_address` should be **hashed or truncated** before storage.
- `date_of_birth` should be converted into **age bands** (e.g., 18–25, 26–35).

These measures support the following GDPR principles:
- **GDPR Article 5(1)(c)** — Data Minimization  
- **GDPR Article 25** — Data Protection by Design and by Default

Pseudonymization reduces the likelihood of direct identification while still allowing the dataset to be used for analytical purposes.

## 4. AI Risk Management
Because the credit scoring system performs automated decision-making that may impact individuals’ financial opportunities, structured risk management is required.

The organization should:
- Identify risks associated with automated credit decisions.
- Assess the potential impact on individuals and protected groups.
- Define mitigation strategies such as bias monitoring and model validation.
- Continuously monitor model performance and fairness.

Regular evaluation of model outcomes helps ensure that automated decisions remain accurate, fair, and compliant with regulatory requirements.

## 5. Accountability & Documentation
Clear governance structures and documentation practices are necessary to ensure transparency and regulatory compliance.

The organization should implement:
- **Traceability of model development and updates** through version control systems such as Git.
- **Clearly defined roles and responsibilities** for data scientists, compliance officers, and data protection officers.
- **Documentation of automated decisions**, including model inputs, outputs, and decision logic.

These practices support transparency and enable audits or regulatory reviews when required.

## 6. Human Oversight
Because automated credit decisions can significantly affect individuals, **human oversight mechanisms must be implemented**.

Applicants should have the right to request **human review of automated decisions**, in accordance with **GDPR Article 22** and the requirements for high-risk AI systems under the **EU AI Act**.

Human reviewers must be able to:
- reassess automated model decisions,
- examine the factors influencing the decision,
- override or confirm the outcome when necessary.

Human oversight ensures that automated systems remain accountable and that individuals are protected from unfair or incorrect algorithmic decisions.