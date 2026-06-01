# Part 1 — Data Audit, EDA & Business Understanding

## Objective

This repository contains Part 1 of the churn analysis project. The goal of this part is to inspect the raw data, identify data-quality issues, perform exploratory data analysis, and convert the findings into business-facing churn-risk hypotheses.

## Files Included

- `eda_audit.ipynb` — full notebook containing data loading, schema inspection, joins, charts, and churn-risk hypotheses
- `data_quality_report.md` — summary of missing values, duplicates, outliers, join issues, date consistency issues, and leakage risks
- `business_memo.md` — short business-facing memo explaining what the company should investigate before launching a retention campaign
- `requirements.txt` — Python libraries required to run the notebook

## Dataset Files Used

This notebook uses the following raw files from the dataset package:

- `customers.csv`
- `orders.csv`
- `support_tickets.csv`
- `web_events_snapshot.csv`
- `churn_labels.csv`
- `intervention_history.csv`
- `rfm_modeling_snapshot.csv`

## Setup Instructions

1. Download the dataset files and place them in the same folder as the notebook.
2. Create a virtual environment.
3. Install dependencies from `requirements.txt`.
4. Open the notebook in VS Code or Jupyter and run all cells.

## Commands

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

If using Windows:

```bash
venv\Scripts\activate
pip install -r requirements.txt
```

## Notes

- The notebook uses only pre-snapshot order data for feature analysis wherever leakage is a concern.
- Duplicate-like order rows with `_DUP` suffix are treated as data-quality noise and excluded from clean aggregations.
- The final notebook includes at least 6 meaningful charts/tables and 5 churn-risk hypotheses supported by evidence.