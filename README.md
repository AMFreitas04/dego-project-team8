# DEGO Project - Team 8

## Team Members
- **Afonso Freitas**:
- **Romeo Heukamp**: Data Engineer

## Project Description
Credit scoring bias analysis for DEGO course [cite: 3, 18-19].

## Structure
- `data/` - Dataset files [cite: 102]
- `notebooks/` - Jupyter analysis notebooks [cite: 104]
- `src/` - Python source code [cite: 107]
- `reports/` - Final deliverables [cite: 108]


## Data Engineering & Quality Report
We performed a structured data quality assessment on the raw dataset (502 records) to ensure a reliable foundation for downstream bias analysis [cite: 19, 125-127].

### Data Quality Dimensions & Remediation Mapping
| Dimension | Issue Identified | Remediation Action |
| :--- | :--- | :--- |
| **Uniqueness** | 2 Duplicate records found (0.40%) | Dropped duplicates based on `_id` |
| **Completeness** | Identified 1% missingness in SSN, IP Address, and Annual Income fields | Identified gaps for governance review |
| **Completeness** | 87.6% missing `processing_timestamp` and 90% `loan_purpose` | Flagged as a Governance/Logging gap |
| **Consistency** | Mixed types in `annual_income` (str/int/float) | Standardized all values to `float` |
| **Consistency** | 6 variations of Gender coding (M, Male, F, etc.) | Standardized to `['Male', 'Female', nan]` |
| **Validity** | 2 records with negative credit months | Set invalid values to `NaN` (Logical Impossibility) |
| **Accuracy** | Mixed date formats (ISO vs European) | Unified all to `YYYY-MM-DD` datetime objects |