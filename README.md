# DEGO Project - Team 8

## Team Members
- **John Eke**: Product Lead
- **Afonso Freitas**: Data Scientist
- **Romeo Heukamp**: Data Engineer
- **Afonso Alves**: Governance Officer

## Executive Summary
In this project, we audited NovaCred’s credit decision data and pipeline from three angles: data quality, fairness, and privacy/compliance.

We started with **502 records** and, after remediation, ended with **501 records**. Most core checks now pass, but identity-conflict cases were intentionally kept and flagged for governance review.

## Key Findings and Metrics

### 1) Data Quality (`notebooks/01 - data - qualityv2.ipynb`)
| Dimension | Metric | Result |
|---|---|---|
| Uniqueness | Duplicate `_id` groups | 2 (`app_042`, `app_001`) |
| Uniqueness | Repeated non-null SSNs | 3 |
| Uniqueness | SSNs linked to multiple names | 2 |
| Consistency | `annual_income` type drift | int 488, str 8, float 6 |
| Consistency | Gender coding variations | `Male`, `M`, `F`, `Female`, `''` |
| Consistency | Invalid emails | 4 |
| Consistency | DOB not in target ISO format (pre-standardization) | 157 |
| Consistency | Decision cross-field violations | 0 |
| Completeness | Missing `applicant_info.ssn` | 1.0% |
| Completeness | Missing `applicant_info.ip_address` | 1.0% |
| Completeness | Missing `financials.annual_income` | 1.0% |
| Completeness | Missing `loan_purpose` | 90.0% |
| Completeness | Missing `processing_timestamp` | 87.6% |
| Validity | Negative `credit_history_months` | 2 |
| Validity | Negative `savings_balance` | 1 |
| Validity | Zero `annual_income` | 1 |
| Accuracy | DOB plausibility failures (<18, >100, future) | 0 |

### Remediation Outcomes Implemented
- Duplicate handling reduced records from **502 -> 501**.
- Identity conflicts were preserved and flagged via `identity_conflict` for manual review.
- `annual_income` was standardized to numeric and backfilled from `annual_salary` where applicable.
- Gender labels were standardized to canonical values.
- Email quality was retained through `email_valid` instead of silent auto-correction.
- DOB values were standardized to `YYYY-MM-DD` after mixed-format parsing.
- Validity fixes removed negative/zero violations for targeted fields.

### 2) Bias and Fairness (`notebooks/02 - bias - analysis.ipynb`)
We ran the bias audit on **501 cleaned records** and analyzed **15 flattened spending features**.

- **Direct bias (80% rule)**
  - Male approval rate: **0.659**
  - Female approval rate: **0.508**
  - Disparate Impact (Female/Male): **0.771**
  - This is below the 0.80 threshold, so the result indicates adverse impact.

- **Geographic proxy signal**
  - ZIP female-density vs approval correlation: **-0.193**
  - This is a weak correlation, but the negative direction still deserves monitoring for potential redlining patterns.

- **Spending-category signal**
  - `rent` shows the lowest approval rate among spending categories, with female presence around 50%.
  - `gambling` has the highest female presence (around 85%) with moderate approval (~57–58%).
  - `alcohol` and `insurance` show higher approval rates (>70%) and are more male-associated.
  - No category crossed the strict red-flag rule (`female_presence > 60%` and `approval_rate < 40%`), but the pattern still suggests potential proxy effects.

### 3) Privacy, GDPR, and EU AI Act (`notebooks/03-privacy-demo.ipynb`)
The privacy review mapped direct identifiers (for example, SSN and email) versus indirect identifiers, and demonstrated pseudonymization with hashing.

From a GDPR perspective, the core issue is a pattern of weak controls:
- **Lawfulness, Fairness & Transparency**: missing `processing_timestamp` (**87.6%**) and `loan_purpose` (**90.0%**) reduce explainability of automated decisions.
- **Purpose Limitation**: heavy missingness in `loan_purpose` (**90.0%**) weakens evidence of clear processing purpose.
- **Data Minimization**: highly granular spending-behavior attributes may go beyond strict necessity for credit decisions.
- **Accuracy**: mixed DOB formats, mixed `annual_income` types, and invalid negative values reduce decision-data reliability.
- **Storage Limitation**: missing `processing_timestamp` (**87.6%**) prevents reliable retention-window enforcement.
- **Integrity & Confidentiality**: partial missingness in sensitive fields (`ssn`, `ip_address`) indicates inconsistent handling controls.
- **Accountability**: duplicate `_id` records, repeated SSNs, and identity conflicts indicate weak governance controls and limited auditability.

Under the EU AI Act context, credit scoring is a **high-risk** use case. That means these are not just technical cleanup issues; they are compliance risks that need governance ownership.

## Actionable Governance Recommendations
1. **Treat identity conflicts as cases, not just data errors.**
Implement a mandatory manual-review flow for SSN/name conflicts before records enter model training or production scoring.

2. **Enforce data contracts at ingestion.**
Block or quarantine records that fail schema/type requirements (income types, DOB format, controlled categorical values).

3. **Make traceability mandatory.**
Require `processing_timestamp` and `loan_purpose` for all decision events. Missing metadata should block production scoring or require approved override logging.

4. **Add fairness gates to model release.**
Use DI thresholds and subgroup monitoring as go/no-go criteria, and review proxy-sensitive features like ZIP and selected spending categories.

5. **Implement privacy-by-design controls.**
Minimize direct identifiers in analytics/modeling layers and apply tokenization/pseudonymization with strict access controls.

6. **Run ongoing monitoring with escalation rules.**
Publish recurring quality/fairness/compliance KPIs and define SLA-based remediation ownership in Governance.

## Repository Structure
- `data/` - Dataset files
- `notebooks/` - Analysis notebooks
- `presentation/` - Video presentation