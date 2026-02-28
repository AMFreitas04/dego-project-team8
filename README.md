# DEGO Project - Team 8

## Team Members
- **Afonso Freitas**: Data Scientist
- **Romeo Heukamp**: Data Engineer
- **Afonso Alves**: Governance Officer

## Project Description
Credit scoring bias analysis for DEGO course.

## Structure
- `data/` - Dataset files.
- `notebooks/` - Jupyter analysis notebooks
- `src/` - Python source code 
- `reports/` - Final deliverables


## Data Engineering & Quality Report
We performed a structured data quality assessment on the raw dataset (502 records) to ensure a reliable foundation for downstream bias analysis.

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
