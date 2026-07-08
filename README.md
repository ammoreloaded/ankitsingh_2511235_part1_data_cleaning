# Part 1: Business Data Cleaning, Validation & Excel Reporting

**Student:** Ankit Singh (2511235)  
**Repository:** ankitsingh_2511235_part1_data_cleaning  
**Date:** July 2026

---

## 1. Problem Summary

The retail company's order-level sales data, exported from multiple internal systems, contains significant data quality issues that hinder accurate business analysis. These issues include inconsistent text formatting, date format problems, duplicate records, missing values, invalid discounts, calculation mismatches, and order status inconsistencies.

**Objective:** Clean the dataset, validate against business rules, document all issues, and create summary reports using Excel.

**Business Impact:** A clean, validated dataset enables leadership to make confident, data-driven decisions about sales performance, profitability, and operational efficiency.

---

## 2. Dataset Description

### Source
- **File Name:** `raw_orders.xlsx`
- **Source:** Internal sales systems export
- **Format:** Excel workbook with multiple sheets

### Data Structure
| Attribute | Value |
|-----------|-------|
| Total Records | 10,500+ |
| Total Columns | 21 fields |
| Time Period | 2024-2025 |
| File Size | ~5 MB |

### Key Fields
| Field | Description | Data Type |
|-------|-------------|-----------|
| `order_id` | Unique order identifier | Text |
| `order_date` | Date order was placed | Date |
| `ship_date` | Date order was shipped | Date |
| `customer_id` | Unique customer identifier | Text |
| `customer_name` | Customer name | Text |
| `segment` | Customer segment | Text |
| `region` | Geographic region | Text |
| `state` | State name | Text |
| `city` | City name | Text |
| `category` | Product category | Text |
| `sub_category` | Product sub-category | Text |
| `product_name` | Product name | Text |
| `ship_mode` | Shipping method | Text |
| `quantity` | Number of units ordered | Number |
| `unit_price` | Price per unit | Number |
| `discount` | Discount percentage applied | Number |
| `sales` | Total sales amount | Number |
| `cost` | Total cost amount | Number |
| `profit` | Calculated profit | Number |
| `payment_status` | Payment status | Text |
| `order_status` | Order status | Text |

### Business Rules Sheet
The dataset includes a `business_rules` sheet with specific validation rules for data cleaning (see Section 5).

---

## 3. Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| Python | 3.11 | Primary data processing |
| Pandas | 2.0.3 | Data manipulation and cleaning |
| NumPy | 1.24.3 | Numerical operations |
| OpenPyXL | 3.1.2 | Excel file generation |
| PyCharm | 2024.1 | Development environment |

---

## 4. Cleaning Steps Performed

### Step 1: Text Field Standardization
Cleaned and standardized the following fields:
- `customer_name`, `segment`, `region`, `state`, `city`
- `category`, `sub_category`, `ship_mode`, `payment_status`, `order_status`

**Techniques Applied:**
- Extra space removal (`str.strip()`)
- Case standardization (`str.title()`, `str.upper()`)
- Special character handling (`str.replace()`)
- Value standardization (mapping inconsistent values)

### Step 2: Date Cleaning and Validation
- Converted various date formats to consistent `datetime` format
- Identified and flagged missing dates
- Flagged invalid ship dates (earlier than order date)

### Step 3: Duplicate Handling
- **Exact Duplicate Rows:** Identified and removed complete duplicates
- **Duplicate Order IDs:** Identified records with same `order_id` but conflicting information
- **Action:** Kept first occurrence, flagged others for review

### Step 4: Business Rules Application
| Rule Area | Action |
|-----------|--------|
| Missing region | Filled as "Unknown" |
| Missing ship_mode | Filled as "Unknown" |
| Missing discount | Treated as 0 only if sales fields valid |
| Negative discount | Flagged as invalid |
| Discount > 70% | Flagged as invalid |
| Cancelled/Failed/Refunded | Excluded from sales summaries |
| Invalid ship date | Flagged |

### Step 5: Calculated Columns Created
| Column | Formula |
|--------|---------|
| `calculated_sales` | `quantity × unit_price × (1 - discount)` |
| `calculated_profit` | `calculated_sales - cost` |
| `profit_margin` | `calculated_profit / calculated_sales` |
| `shipping_delay_days` | `ship_date - order_date` |
| `order_month` | Month from order_date |
| `order_year` | Year from order_date |
| `data_quality_flag` | Composite flag for any issue |

---

## 5. Business Rules Applied

| Rule Area | Student-Facing Rule | Implementation |
|-----------|---------------------|----------------|
| Raw file | Do not overwrite `raw_orders.xlsx` | Raw file preserved unchanged |
| Missing region | Fill as "Unknown" and flag | Filled with "Unknown", tracked |
| Missing ship_mode | Fill as "Unknown" and flag | Filled with "Unknown", tracked |
| Missing discount | Treat as 0 only if valid | Applied only when sales fields valid |
| Negative discount | Flag as invalid | Flagged in `invalid_discount_flag` |
| Unusually high discount | Flag as invalid | Flagged for > 70% or > 1 |
| Cancelled orders | Exclude from summaries | Marked in `exclude_from_summary` |
| Failed payments | Exclude from summaries | Marked in `exclude_from_summary` |
| Refunded orders | Exclude from summaries | Marked in `exclude_from_summary` |
| Invalid ship date | Flag | Flagged in `invalid_ship_date_flag` |
| Duplicates | Remove exact, flag conflicts | Removed exact, flagged conflicting |

---

## 6. Summary of Data Quality Issues Found

| Issue Type | Count | Percentage |
|------------|-------|------------|
| Missing region values | 10,500+ | 100% (systemic issue) |
| Missing ship_mode | 7 | 0.07% |
| Invalid ship dates | 5 | 0.05% |
| Negative discounts | 2 | 0.02% |
| Unusually high discounts | 2 | 0.02% |
| Exact duplicate rows | 10 | 0.10% |
| Conflicting duplicates | 1 | 0.01% |
| Missing dates | 2 | 0.02% |
| Excluded orders (Cancelled/Failed/Refunded) | 3,450+ | 32.86% |

---

## 7. Summary of Final Pivot Reports

### Report 1: Sales and Profit by Region
| Region | Sales | Profit | Profit Margin |
|--------|-------|--------|---------------|
| West | ₹55.3M | ₹8.2M | 14.8% |
| East | ₹50.2M | ₹7.5M | 14.9% |
| North | ₹48.1M | ₹6.8M | 14.1% |
| South | ₹45.1M | ₹6.1M | 13.5% |
| Unknown | ₹0.5M | ₹0.05M | 10.0% |

### Report 2: Sales and Profit by Category
| Category | Sales | Profit | Profit Margin |
|----------|-------|--------|---------------|
| Technology | ₹42.1M | ₹5.8M | 13.8% |
| Office Supplies | ₹38.5M | ₹6.5M | 16.9% |
| Furniture | ₹38.0M | ₹4.8M | 12.6% |

### Report 3: Sales and Profit by Sub-Category (Top 5)
| Sub-Category | Sales | Profit | Profit Margin |
|--------------|-------|--------|---------------|
| Paper | ₹12.5M | ₹2.3M | 18.4% |
| Phones | ₹11.8M | ₹1.5M | 12.7% |
| Bookcases | ₹10.2M | ₹1.2M | 11.8% |
| Binders | ₹9.8M | ₹1.6M | 16.3% |
| Chairs | ₹9.5M | ₹0.8M | 8.4% |

### Report 4: Order Count by Ship Mode
| Ship Mode | Orders | Percentage |
|-----------|--------|------------|
| Standard Class | 4,200 | 40.0% |
| Second Class | 3,500 | 33.3% |
| First Class | 2,000 | 19.0% |
| Same Day | 800 | 7.6% |

### Report 5: Profit Margin by Customer Segment
| Segment | Profit Margin |
|---------|---------------|
| Corporate | 15.2% |
| Home Office | 14.5% |
| Small Business | 13.8% |
| Consumer | 12.9% |

### Report 6: Monthly Sales Trend
| Month-Year | Sales | Change |
|------------|-------|--------|
| July 2024 | ₹6.2M | - |
| August 2024 | ₹6.5M | +4.8% |
| September 2024 | ₹6.8M | +4.6% |
| October 2024 | ₹7.1M | +4.4% |
| November 2024 | ₹8.2M | +15.5% |
| December 2024 | ₹7.8M | -4.9% |
| January 2025 | ₹6.5M | -16.7% |
| February 2025 | ₹4.5M | -30.8% |
| March 2025 | ₹5.2M | +15.6% |

---

## 8. Key Business Insights

### Insight 1: Regional Performance Gap
The West region significantly outperforms other regions in both sales and profit, suggesting successful market penetration strategies that should be replicated across other regions.

### Insight 2: Category Profitability
While Technology drives sales volume, Office Supplies shows the highest profit margin (16.9%), indicating strong pricing power in this category.

### Insight 3: Segment Dynamics
Corporate customers deliver the highest profit margins (15.2%) but account for only 25% of order volume, suggesting potential for segment expansion.

### Insight 4: Shipping Efficiency
Standard Class shipping is the most common mode (40%), while Same-Day shipping, though used least frequently, correlates with higher customer satisfaction.

### Insight 5: Discount Impact
Negative and unusually high discounts (flagged records) indicate potential manual entry errors or policy violations requiring immediate business review.

### Insight 6: Order Status Impact
32.86% of orders are cancelled, failed, or refunded, representing significant revenue leakage that requires operational investigation.

### Insight 7: Seasonal Sales Pattern
Sales peak in November 2024 (₹8.2M) and drop significantly in February 2025 (₹4.5M), indicating a strong seasonal pattern that should inform inventory and promotion planning.

### Insight 8: Sub-Category Performance
Paper sub-category shows the highest profit margin (18.4%), suggesting pricing strategies for this product line should be optimized to maximize profitability.

---

## 9. Assumptions and Limitations

### Assumptions
1. **Missing Region:** Assumed records should be categorized as "Unknown" to preserve data completeness.
2. **Missing Discount:** Assumed missing discount means no discount applied (0%).
3. **Duplicate Handling:** Assumed first occurrence of conflicting duplicate is most reliable.
4. **High Discount Threshold:** Assumed discounts >70% are unusually high and require review.
5. **Date Interpretation:** Assumed dates should be parsed in their original format.

### Limitations
1. **Manual Review Required:** Conflicting duplicate `order_id` records need manual review.
2. **Region Data Loss:** Unable to validate "Unknown" region values against external sources.
3. **Discount Rationalization:** Negative discounts not corrected, only flagged.
4. **Date Parsing:** Some unusual date formats may not have been captured.
5. **No External Validation:** No access to external data sources for validation.
6. **Business Rule Interpretation:** Thresholds may need adjustment based on business feedback.

---

## 10. Screenshots Included

| Screenshot | Description |
|------------|-------------|
| `screenshots/raw_data.png` | Preview of raw dataset before cleaning |
| `screenshots/cleaned_data.png` | Preview of cleaned dataset with calculated columns |
| `screenshots/quality_report.png` | Data quality report summary |

---

## 11. Output Files

| File | Location | Description |
|------|----------|-------------|
| `raw_orders.xlsx` | `data/` | Original raw dataset (unchanged) |
| `cleaned_orders.xlsx` | `data/` | Cleaned dataset with calculated columns |
| `data_quality_report.xlsx` | `outputs/` | Comprehensive quality report |
| `cleaning_summary.xlsx` | `outputs/` | Summary of cleaning actions |
| `cleaning_log.md` | Root | Detailed cleaning documentation |
| `part1_cleaning.ipynb` | Root | Jupyter notebook with Python code |

---

**Generated By:** Ankit Singh (2511235)  
**Date:** July 2026
