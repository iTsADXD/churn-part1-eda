# Data Quality Report

**Project:** Part 1 — Data Audit, EDA & Business Understanding  
**Snapshot Date:** 2025-09-30

---

## 1. Files Reviewed

The following raw files were inspected:

- `customers.csv`
- `orders.csv`
- `support_tickets.csv`
- `web_events_snapshot.csv`
- `churn_labels.csv`
- `intervention_history.csv`
- `rfm_modeling_snapshot.csv`

---

## 2. Missing Values

The main missing-value issues observed were:

- `loyalty_tier` in `customers.csv` has a large number of missing values.
- `skin_type` in `customers.csv` also contains missing records.
- `rating` in `orders.csv` has a small number of nulls.

### Impact

- Missing loyalty information may hide whether loyalty membership is related to churn.
- Missing skin type is less critical for churn modelling, but it affects customer profile completeness.
- Missing rating values slightly reduce the reliability of satisfaction-related order analysis.

### Treatment Recommendation

- Fill missing `loyalty_tier` with `"Not Enrolled"` or `"Unknown"` depending on analysis purpose.
- Fill missing `skin_type` with `"Unknown"` if needed for grouping.
- Use median imputation or leave missing ratings excluded when computing average ratings.

---

## 3. Duplicate or Duplicate-Like Records

The `orders.csv` file contains duplicate-like records where `order_id` ends with `_DUP`.

### Impact

- These rows inflate order counts, revenue totals, and customer frequency if not removed.
- They can distort both EDA and downstream modelling.

### Treatment Recommendation

- Remove all `_DUP` rows before calculating customer-level order features.

---

## 4. Invalid or Unusual Values

A few unusual values were found:

- Very high `gross_amount` values compared with the general order distribution.
- Long `resolution_hours` values in support tickets.
- Customers with no pre-snapshot orders but still present in the customer master table.

### Impact

- Extremely high order values may distort averages.
- Long resolution times may indicate genuine escalation issues, so they should not be removed blindly.
- Customers with no orders need special handling in recency and frequency analysis.

### Treatment Recommendation

- Flag unusual values first instead of deleting them immediately.
- Retain business-valid extremes if they reflect real behaviour.
- For no-order customers, use a fallback recency value and mark them clearly in analysis.

---

## 5. Outliers

Observed outlier areas:

- `gross_amount`
- `resolution_hours`
- `recency_days` after aggregation

### Impact

- Outliers can skew mean-based summaries and may exaggerate business patterns.
- Some outliers may be meaningful and represent premium or problematic customers.

### Treatment Recommendation

- Use descriptive statistics and visual inspection before trimming.
- Prefer median and bucket-based summaries in EDA where needed.
- Retain outliers if they are realistic and useful for churn-risk understanding.

---

## 6. Join / Key Issues

Customer IDs were checked across files.

### Findings

- Core customer IDs align across `customers.csv`, `churn_labels.csv`, and `rfm_modeling_snapshot.csv`.
- Some customers appear in the customer base without orders or support tickets, which is valid.
- No major orphan-key issue was found in the main joins.

### Treatment Recommendation

- Use left joins from the customer master table to preserve the full customer universe.
- Fill missing order/support aggregates with zero where appropriate.

---

## 7. Date Consistency Issues

The biggest date-related issue is that `orders.csv` contains both:

- rows on or before the snapshot date
- rows after the snapshot date

### Impact

- Post-snapshot rows can leak future information into EDA or model features if used incorrectly.
- This would make churn analysis unrealistic and over-optimistic.

### Treatment Recommendation

- Restrict feature engineering and behavioural analysis to orders with `order_date <= 2025-09-30`.
- Keep post-snapshot rows only for understanding target construction, not for predictors.

---

## 8. Leakage Risk Columns

The following fields can cause leakage if used incorrectly:

- post-snapshot order rows in `orders.csv`
- `churn_next_60d`
- `split`
- any future-derived customer feature calculated after the snapshot date

### Impact

- Leakage would produce misleading churn-risk patterns and invalid conclusions.

### Treatment Recommendation

- Use snapshot-safe data only.
- Keep `churn_next_60d` only as the outcome label.
- Exclude `split` from analysis features.
- Audit all date-based calculations carefully.

---

## 9. Summary

The dataset is usable, but only after careful treatment of:

- missing loyalty/profile values,
- duplicate-like order rows,
- unusual order and ticket values,
- snapshot leakage from post-2025-09-30 orders.

The most important data-quality risk is future leakage from orders placed after the snapshot date. The most important cleaning step is removing `_DUP` order rows before aggregating customer behaviour.