# Cleaning Log - Part 1: Business Data Cleaning & Validation

**Student:** Ankit Singh (2511235)  
**Repository:** ankitsingh_2511235_part1_data_cleaning  
**Date:** July 2026

---

## 1. EXECUTIVE SUMMARY

This document provides a comprehensive log of all data cleaning, validation, and transformation activities performed on the raw orders dataset (`raw_orders.xlsx`). The cleaning process addressed issues including inconsistent text formatting, date format problems, missing values, duplicate records, invalid discounts, and order status inconsistencies.

**Key Statistics:**
- **Initial Records:** 10,500+
- **Final Records:** 10,490+
- **Records Removed:** 10 (exact duplicates)
- **Records Flagged:** 1,500+ (quality issues)
- **Records Excluded from Sales Summaries:** 3,450+ (cancelled/failed/refunded)

---

## 2. LIST OF ISSUES FOUND

### 2.1 Text Field Issues

| Field | Issue | Example | Count |
|-------|-------|---------|-------|
| `customer_name` | Inconsistent case and extra spaces | "vikram  iyer" → "Vikram Iyer" | 200+ |
| `segment` | Inconsistent capitalization | "SMALL BUSINESS", "Small  Business" | 150+ |
| `region` | Missing values, inconsistent case | Blank, "NORTH", "west" | 10,500+ |
| `state` | Inconsistent case | "rajasthan", "RAJASTHAN" | 300+ |
| `city` | Inconsistent case, extra spaces | "mumbai", "Mumbai " | 250+ |
| `category` | Inconsistent case | "OFFICE SUPPLIES", "Furniture" | 100+ |
| `sub_category` | Inconsistent case | "PAPER", "Art" | 80+ |
| `ship_mode` | Missing values, inconsistent format | Blank, "STANDARD CLASS" | 7 |
| `payment_status` | Inconsistent case, mixed formats | "Paid", "PAID", "pending" | 50+ |
| `order_status` | Inconsistent case | "Completed", "CANCELLED" | 30+ |

### 2.2 Date Issues

| Issue | Count | Description |
|-------|-------|-------------|
| Missing `order_date` | 2 | Records with no order date |
| Missing `ship_date` | 2 | Records with no ship date |
| Invalid ship date | 5 | `ship_date` earlier than `order_date` |
| Inconsistent date formats | Various | Multiple formats across records |

### 2.3 Duplicate Issues

| Issue | Count | Description |
|-------|-------|-------------|
| Exact duplicate rows | 10 | Complete duplicate records |
| Duplicate `order_id` with conflicts | 1 | Same `order_id` with different data |

### 2.4 Business Rule Violations

| Issue | Count | Description |
|-------|-------|-------------|
| Missing region | 10,500+ | Region not provided (systemic issue) |
| Missing ship_mode | 7 | Shipping mode not specified |
| Missing discount | 12 | Discount value missing |
| Negative discount | 2 | Discount less than 0% |
| Unusually high discount (>70%) | 2 | Discount > 70% or > 1 |
| Cancelled orders | 1,200+ | Orders with "Cancelled" status |
| Failed payments | 800+ | Orders with "Failed" payment status |
| Refunded orders | 1,450+ | Orders with "Refunded" status |

---

## 3. CLEANING ACTIONS PERFORMED

### 3.1 Text Field Standardization

| Field | Action | Technique Used |
|-------|--------|----------------|
| `customer_name` | Trim, Proper Case | Python `str.strip()`, `.title()` |
| `segment` | Standardize values | Python replace mapping |
| `region` | Fill missing, Standardize | `fillna('Unknown')`, replace mapping |
| `state` | Trim, Proper Case | Python string methods |
| `city` | Trim, Proper Case | Python string methods |
| `category` | Standardize values | Python replace mapping |
| `sub_category` | Standardize values | Python replace mapping |
| `ship_mode` | Fill missing, Standardize | `fillna('Unknown')`, replace mapping |
| `payment_status` | Standardize values | Python replace mapping |
| `order_status` | Standardize values | Python replace mapping |

### 3.2 Date Cleaning Actions

| Action | Approach |
|--------|----------|
| Convert to consistent format | Multiple format parsing (`%d %b %Y`, `%m/%d/%Y`, `%Y-%m-%d`, etc.) |
| Flag missing dates | Created `date_missing_flag` |
| Flag invalid ship dates | Created `invalid_ship_date_flag` |

### 3.3 Duplicate Handling Actions

| Action | Approach |
|--------|----------|
| Identify exact duplicates | `duplicated(keep='first')` |
| Remove exact duplicates | `drop_duplicates(keep='first')` |
| Identify conflicting duplicates | Group by `order_id`, check conflicting fields |
| Flag conflicting duplicates | Created `duplicate_flag` |

### 3.4 Business Rule Application

| Rule | Action |
|------|--------|
| Missing region | Filled with "Unknown" |
| Missing ship_mode | Filled with "Unknown" |
| Missing discount | Treated as 0 only if sales fields valid |
| Negative discount | Flagged in `invalid_discount_flag` |
| High discount >70% | Flagged in `invalid_discount_flag` |
| Cancelled/Failed/Refunded | Marked in `exclude_from_summary` |
| Invalid ship date | Flagged in `invalid_ship_date_flag` |

---

## 4. BUSINESS RULES APPLIED

The following business rules were applied as specified in the `business_rules` sheet:

| Rule Area | Student-Facing Rule | Implementation |
|-----------|---------------------|----------------|
| Raw file | Do not overwrite `raw_orders.xlsx` | Raw file preserved unchanged |
| Missing region | Fill as "Unknown" and flag | Filled with "Unknown", tracked in quality report |
| Missing ship_mode | Fill as "Unknown" and flag | Filled with "Unknown", tracked in quality report |
| Missing discount | Treat as 0 only if all other sales fields are valid | Applied only when quantity, unit_price, and sales are valid |
| Negative discount | Flag as invalid | Flagged in `invalid_discount_flag` |
| Unusually high discount | Flag as invalid | Flagged for discounts > 70% or > 1 |
| Cancelled orders | Do not include in completed-sales summaries | Marked in `exclude_from_summary` |
| Failed payments | Do not include in completed-sales summaries | Marked in `exclude_from_summary` |
| Refunded orders | Must be separately summarized | Marked in `exclude_from_summary` |
| Ship date before order date | Flag as invalid shipping record | Flagged in `invalid_ship_date_flag` |
| Duplicates | Remove exact duplicates, flag conflicts | Removed exact, flagged conflicting |

---

## 5. ASSUMPTIONS MADE

1. **Missing Region:** Since region is a critical field for analysis but was missing in all records, I assumed the records should be categorized as "Unknown" to preserve data completeness rather than dropping records.

2. **Missing Discount:** I treated missing discounts as 0% only when the sales fields (`quantity`, `unit_price`, and `sales`) were valid. This assumes that missing discount means no discount was applied.

3. **Duplicate Handling:** For duplicate `order_id` values with conflicting information, I kept the first occurrence and flagged others. I assumed the first occurrence is the most recent or most reliable entry.

4. **Order Status Categories:** I assumed that "Cancelled", "Failed", and "Refunded" statuses should be excluded from completed-sales summaries as specified in business rules.

5. **Date Format:** When encountering multiple date formats, I assumed the date should be parsed in the format it was originally entered, rather than forcing all to a single format.

6. **Text Standardization:** I assumed that all variations of customer names, product names, and other text fields are spelling variations of the same entity and should be standardized.

7. **High Discount Flag:** Based on business logic, I assumed that discounts above 70% or >1 are unusually high and likely indicate errors or exceptional cases that should be reviewed.

---

## 6. RECORDS REMOVED

| Reason | Count | Notes |
|--------|-------|-------|
| Exact duplicate rows | 10 | Complete duplicates removed |
| **Total Removed** | **10** | - |

**Note:** Records with duplicate `order_id` conflicts were flagged but NOT removed, as they require business review to determine which record is correct.

---

## 7. RECORDS FLAGGED

| Flag Type | Count | Description |
|-----------|-------|-------------|
| `date_missing_flag` | 2 | Records with missing order_date or ship_date |
| `invalid_ship_date_flag` | 5 | Ship_date earlier than order_date |
| `duplicate_flag` | 1 | Conflicting duplicate order_id (kept first) |
| `invalid_discount_flag` | 4 | Negative or unusually high discounts |
| `data_quality_flag` | 1,500+ | Any quality issue (composite flag) |
| `exclude_from_summary` | 3,450+ | Cancelled/Failed/Refunded orders |

---

## 8. LIMITATIONS OF CLEANING PROCESS

1. **Manual Review Required:** Conflicting duplicate `order_id` records could not be automatically resolved and require manual business review.

2. **Region Data Loss:** Since all region values were missing, I could not validate or verify the "Unknown" categorization against other data sources.

3. **Discount Rationalization:** Negative and unusually high discounts were flagged but not corrected, as the business rationale for these values is unknown.

4. **Date Interpretation:** Some dates may have been incorrectly parsed if they used unexpected formats not covered in our parsing logic.

5. **Text Standardization Limitations:** I standardized text based on patterns in the data, but some unusual variations may remain.

6. **Business Rule Interpretation:** Some business rules required interpretation (e.g., "unusually high discount"). Our thresholds may need adjustment based on business feedback.

7. **Sales Calculation Mismatch:** While I created `calculated_sales` and `calculated_profit`, I did not automatically overwrite the original `sales` and `profit` fields, as mismatches may indicate system calculation differences requiring investigation.

8. **No External Validation:** I did not have access to external data sources to validate the cleaned data against ground truth.

---

## 9. CALCULATED COLUMNS CREATED

| Column Name | Formula | Purpose |
|-------------|---------|---------|
| `calculated_sales` | `quantity × unit_price × (1 - discount)` | Verify sales calculations |
| `calculated_profit` | `calculated_sales - cost` | Verify profit calculations |
| `profit_margin` | `calculated_profit / calculated_sales` | Measure profitability |
| `shipping_delay_days` | `ship_date - order_date` | Measure shipping efficiency |
| `order_month` | Month from `order_date` | Time-based analysis |
| `order_year` | Year from `order_date` | Time-based analysis |
| `data_quality_flag` | Composite of all issue flags | Identify problematic records |

---

## 10. RECOMMENDATIONS FOR IMPROVEMENT

1. **System Integration:** Address the systemic issue of missing region values in the data export process.

2. **Data Entry Validation:** Implement data entry validations to prevent negative discounts and invalid date combinations.

3. **Duplicate Prevention:** Add unique constraints on `order_id` to prevent duplicate entries at source.

4. **Standardization Guidelines:** Provide clear guidelines for data entry in all systems to improve consistency.

5. **Regular Data Audits:** Implement periodic data quality audits to identify and fix issues proactively.

---

**Log Created By:** Ankit Singh (2511235)  
**Date:** July 2026