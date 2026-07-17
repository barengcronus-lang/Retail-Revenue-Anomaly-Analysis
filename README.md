# South African Retail Sales Analysis — Power BI Portfolio Project
## 📌 Overview

This project investigates a revenue anomaly in a South African retail dataset (2022–2024, ZAR-denominated): Office Supply revenue nearly doubled year-over-year while units sold remained almost flat. Rather than presenting a surface-level dashboard, this report is structured as a hypothesis-driven investigation — testing and ruling out explanations systematically until the true driver was identified.
The end-to-end process covers data cleaning, star-schema data modeling, DAX time-intelligence measures, and a four-page analytical narrative built in Power BI.

## 🎯 The Business Question

In 2023, Office Supply generated R1,757,409 in revenue from 2,010 units sold. In 2024, revenue rose to R3,555,802 — a ~101% increase — while units sold only grew to 2,029, a marginal change. If customers weren't buying significantly more, what was actually driving the revenue growth?
## 🔍 The Investigation

### Hypothesis 1: Discounting

A scatter plot (month × revenue, bubble size = units sold) and a dual-axis discount/units-sold line chart were used to test whether promotional discounting explained the surge.

Finding: Discount rates were nearly identical across both years (January–December). More notably, discounting only showed a measurable relationship with units sold during October–December; from January to early September, discount changes had no visible effect on volume. This hypothesis was ruled out.

### Hypothesis 2: Product Mix & Pricing

With discounting ruled out, a synced matrix and pie chart broke revenue down by subcategory (Labels, Paper, Printer Ink, Binders, Stationery) to identify where the growth was concentrated.

Subcategory 2023 Revenue Share 2024 Revenue Share Labels 4.01%-16.75%  Paper 5.23%-15.41% Printer Ink  7.24%-13.03% Binders 8.56%-10.87% Stationery 8.02%-10.88%

Finding: Labels and Paper showed the sharpest revenue-share growth. Drilling into the product-level hierarchy confirmed the cause: pricing changes and product mix shifts introduced in January 2024, including new product launches and the removal of underperforming SKUs.

## ✅ Findings


Office Supply revenue grew significantly (R1,757,409 → R3,555,802) while units sold remained relatively flat (2,010 → 2,029), indicating growth was not volume-driven
Discount rates were consistent across both years, ruling out promotional activity as the cause
Discounting only correlated with units sold in Q4 (October–Dececember); no measurable effect from January to early September
Labels and Paper drove the largest revenue-share gains, followed by Printer Ink, Binders, and Stationery
Product-level drill-down confirmed pricing changes and product mix shifts (new launches, discontinued underperformers) in January 2024 as the key revenue driver


## 💡 Recommendations


Reduce discount spend from January–September in Office Supplies — no measurable correlation with units sold in this window
Concentrate discount budget in October–December, the only period where discounts and units sold move together
Apply Q4 discounts selectively to underperforming products rather than category-wide, to protect margin
Investigate the specific pricing/mix decisions behind the Labels and Paper growth to inform future category strategy
Assess whether Printer Ink, Binders, and Stationery would benefit from similar pricing or mix strategies
Establish a recurring quarterly review to proactively identify and retire underperforming products
Add a Revenue per Unit YoY measure to catch future price/mix-driven shifts earlier, without a full retrospective investigation

## 🛠️ Technical Process

### 1. Data Ingestion

Data was sourced as CSV and connected via Power BI's Text/CSV connector, then transformed in Power Query before modeling.

### 2. Data Cleaning (Power Query)

The raw dataset contained intentionally realistic data quality issues, resolved through a structured sequence of applied steps:


Inconsistent date formats (2022-12-28, 07/20/2023, 12.12.2022) standardized
Duplicate rows removed
Combined fields split — e.g., "Office Chairs - Standard 158" separated into Sub-Category, Product Name, and Product Code
Text normalization — inconsistent casing and whitespace fixed via Capitalize Each Word, Trim, and Clean Text
Invalid values filtered — e.g., a discount value of "1,5" (150%, clearly erroneous) removed
~24 targeted Replace Value steps to resolve abbreviations and typos (e.g., province codes like "mp", "w. cape", "kzn" mapped to full names)


### 3. Feature Engineering

A calculated column sales (excluding discount) = [Quantity] * [Unit Price] was added to isolate gross sales from discount impact, enabling clean analysis of discount effects independent of pricing.

### 4. Data Modeling — Star Schema

The cleaned fact table (Sales_Fact) was connected via one-to-many relationships to four dimension tables:


Customer_Dim
Product_Dim
Region_Dim
Date_Table (custom-built, including Month_Name, Quarter, and Week via DAX)


### 5. DAX Measures

Month-over-month prior sales:

daxSales PM =
CALCULATE(
    [Total_Revenue],
    DATEADD(Date_Table[Date], -1, MONTH)
)

Month-over-month growth %:

daxMoM Growth % = DIVIDE([MoM Growth], [Sales PM])

Year-over-year revenue %:

daxYoY Revenue % =
VAR CurrentRevenue = [Total_Revenue]
VAR PriorYearRevenue =
    CALCULATE(
        [Total_Revenue],
        SAMEPERIODLASTYEAR(Date_Table[Date])
    )
VAR Result =
    DIVIDE(
        CurrentRevenue - PriorYearRevenue,
        PriorYearRevenue
    )
RETURN Result

### 6. Report Structure — Four-Page Analytical Narrative


Overview — category/subcategory revenue breakdown, YoY headline metrics
Monthly Sales & Discount Analysis — scatter plot and dual-axis discount/units-sold chart to test the discounting hypothesis
Office Supplies: Sub-Category Breakdown — synced matrix and pie chart drilling into revenue, units, and price by subcategory
Findings and Recommendations — narrative conclusions and action items



## 🧰 Tools & Skills Demonstrated


Power Query (ETL/ELT): data cleaning, transformation, column splitting, custom columns
Data Modeling: star-schema design, one-to-many relationships, surrogate keys
DAX: CALCULATE, SAMEPERIODLASTYEAR, DATEADD, DIVIDE, variables
Power BI Service / Report Design: synced visuals, drill-through, matrix hierarchies, multi-page narrative structure
Analytical Method: hypothesis-driven investigation rather than descriptive-only reporting
