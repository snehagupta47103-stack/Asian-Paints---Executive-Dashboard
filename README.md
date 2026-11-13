# Asian Paints – Executive Sales & Performance Dashboard

> A Power BI dashboard that gives leadership a single-page view of revenue, profit, orders and target performance across products, regions and time.

**Page covered:** Executive Overview
**Tool:** Microsoft Power BI Desktop
**Reporting period shown:** 01-Apr-2023 to 31-Mar-2024 (FY 2023-24)
**Currency:** ₹ Crores (Cr)

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Objectives](#2-objectives)
3. [Dashboard Preview](#3-dashboard-preview)
4. [Dataset](#4-dataset)
5. [Data Model](#5-data-model)
6. [DAX Measures](#6-dax-measures)
7. [Dashboard Components](#7-dashboard-components)
8. [Key Insights](#8-key-insights)
9. [Data Notes & Limitations](#9-data-notes--limitations)
10. [How to Use](#10-how-to-use)
11. [Project Structure](#11-project-structure)
12. [Future Scope](#12-future-scope)
13. [Acknowledgment](#13-acknowledgment)
14. [Author](#14-author)

---

## 1. Project Overview

The Executive Overview page summarises the business at a glance. It tracks revenue, profit, margin, order volume and how actual sales compare with targets. Users can slice the data by date range, region, product category, dealer type, state and zone.

---

## 2. Objectives

- Show overall business performance through five headline KPIs.
- Track revenue month by month and compare it with targets.
- Identify which product categories and states contribute the most revenue.
- Provide a category-wise scorecard covering achievement, margin and growth.
- Allow interactive filtering so that any stakeholder can drill into their area of interest.

---

## 3. Dashboard Preview

<img width="1198" height="682" alt="Screenshot 2026-11-13 124821" src="https://github.com/user-attachments/assets/7a5095ea-1466-4418-bdee-1e9e5d663bb4" />

---

## 4. Dataset

Six CSV files are used (star schema: 4 dimension tables and 2 fact tables).

| Table | Type | Description |
|---|---|---|
| FactSales | Fact | Invoice-level sales: quantity, sales amount, discount, net sales, cost and profit |
| FactTargets | Fact | Revenue and profit targets by date, region and product |
| DimDate | Dimension | Calendar from 2022 to 2025 (year, quarter, month, month name) |
| DimProduct | Dimension | Product name, category and sub-category |
| DimRegion | Dimension | Region, zone, state and city |
| DimDealer | Dimension | Dealer name, type, city and state |

**FactSales columns:** InvoiceID, DateKey, ProductID, DealerID, CustomerID, RegionID, Quantity, SalesAmount, DiscountAmount, NetSales, CostAmount, ProfitAmount

**Files not used on this page:** `MarketingCampaigns.csv` and `ProductCostMaster.csv`. Cost and profit are already available in FactSales, and the marketing data has no link to the other tables.

---

## 5. Data Model

Star schema with **FactSales** and **FactTargets** surrounded by shared dimension tables. All relationships are Many-to-One with single-direction filtering.

| From (One side) | To (Many side) |
|---|---|
| DimDate[DateKey] | FactSales[DateKey] |
| DimProduct[ProductID] | FactSales[ProductID] |
| DimDealer[DealerID] | FactSales[DealerID] |
| DimRegion[RegionID] | FactSales[RegionID] |
| DimDate[DateKey] | FactTargets[DateKey] |
| DimRegion[RegionID] | FactTargets[RegionID] |
| DimProduct[ProductID] | FactTargets[ProductID] |

Modelling notes:

- FactSales and FactTargets are not linked directly. They share the dimension tables.
- `CustomerID` is not used because there is no customer dimension.
- DimDate is marked as the Date Table, which supports the previous-year calculations.
- Geography for slicers and the map comes from `DimRegion[State]`, since it is the table linked to sales.

---

## 6. DAX Measures

**Core KPIs**

| Measure | Logic |
|---|---|
| Total Revenue (Cr) | `DIVIDE(SUM(FactSales[NetSales]), 10000000)` |
| Total Profit (Cr) | `DIVIDE(SUM(FactSales[ProfitAmount]), 10000000)` |
| Profit Margin % | `DIVIDE(SUM(FactSales[ProfitAmount]), SUM(FactSales[NetSales]))` |
| Total Orders | `DISTINCTCOUNT(FactSales[InvoiceID])` |
| Revenue Target (Cr) | `DIVIDE(SUM(FactTargets[RevenueTarget]), 10000000)` |
| Target Achievement % | `DIVIDE(SUM(FactSales[NetSales]), SUM(FactTargets[RevenueTarget]))` |

**Previous Year comparison**

| Measure | Logic |
|---|---|
| Revenue PY (Cr) | `CALCULATE([Total Revenue (Cr)], DATEADD(DimDate[Date], -1, YEAR))` |
| Profit PY (Cr) | `CALCULATE([Total Profit (Cr)], DATEADD(DimDate[Date], -1, YEAR))` |
| Orders PY | `CALCULATE([Total Orders], DATEADD(DimDate[Date], -1, YEAR))` |
| Revenue Growth % | `DIVIDE([Total Revenue (Cr)] - [Revenue PY (Cr)], [Revenue PY (Cr)])` |
| Profit Growth % | `DIVIDE([Total Profit (Cr)] - [Profit PY (Cr)], [Profit PY (Cr)])` |
| Orders Growth % | `DIVIDE([Total Orders] - [Orders PY], [Orders PY])` |

**Scorecard**

| Measure | Logic |
|---|---|
| Profit Achievement % | `DIVIDE(SUM(FactSales[ProfitAmount]), SUM(FactTargets[ProfitTarget]))` |

---

## 7. Dashboard Components

**Filters (slicers)**

| Slicer | Field | Location |
|---|---|---|
| Date Range | DimDate[Date] | Header |
| Region | DimRegion[Region] | Header |
| Category | DimProduct[Category] | Sidebar |
| Dealer Type | DimDealer[DealerType] | Sidebar |
| State | DimRegion[State] | Sidebar |
| Zone | DimRegion[Zone] | Sidebar |

**KPI cards**

Total Revenue (Cr), Total Profit (Cr), Profit Margin %, Total Orders, Target Achievement %

**Visuals**

| Visual | Type | Purpose |
|---|---|---|
| Revenue Trend (Monthly) | Line chart | Monthly revenue over the selected period |
| Revenue vs Target (Monthly) | Line and clustered column | Actual revenue against target, with achievement % |
| Revenue by Category | Treemap | Share of revenue by product category |
| Revenue by State | Bar chart | States ranked by revenue |
| Executive Scorecard | Table | Category-wise achievement, margin and growth |

**Navigation:** Overview (active), Regional Performance, Product Analytics, Dealer Performance, Trends & Forecasting.

---

## 8. Key Insights

Figures below are for FY 2023-24 with all slicers set to "All".

1. **Revenue and profit:** The business recorded ₹176.68 Cr in revenue and ₹57.52 Cr in profit from about 75,000 orders.
2. **Healthy margin:** Overall profit margin is about 33%, and it stays within 32–33% across every product category.
3. **Stable monthly revenue:** Revenue is steady at roughly ₹14.2–15.4 Cr a month. December is the peak (₹15.4 Cr) and February is the lowest (₹14.2 Cr).
4. **Top states:** Mizoram records the highest revenue, followed by Himachal Pradesh and West Bengal.
5. **Modest growth:** Revenue growth over the previous year is small, at about 2% overall, and ranges from about 0% to 5% across categories.
6. **Targets are far ahead of actuals:** Revenue Target Achievement is about 9% and Profit Achievement about 15%. See the note below.

---

## 9. Data Notes & Limitations

- **Targets vs sales scale:** Monthly targets in FactTargets are much larger than monthly sales in FactSales, so achievement appears low (around 9%). This comes from the dataset itself and not from the calculation.
- **Dealer Type filter:** FactTargets has no dealer column. The Dealer Type slicer filters sales only, so achievement values can change when it is used.
- **Geography:** Regions, zones and states in the synthetic data do not follow real-world geography (for example, a Tamil Nadu city under "West"). State is used for the map and bar chart.
- **Product categories:** The dataset has seven categories (Putty, Interior Emulsion, Exterior Emulsion, Wood Finish, Industrial Coating, Primer, Waterproofing). They differ from the reference design, which showed Metal Paints and Others.
- **Synthetic data:** The dataset is synthetic and for learning purposes, so distributions look more uniform than real business data.
- **Scope:** Only the Executive Overview page is built. The other navigation items are placeholders.

---

## 10. How to Use

1. Open the `.pbix` file in Power BI Desktop.
2. If data does not load, go to **Transform data > Data source settings** and point each source to the folder that holds the six CSV files.
3. Click **Refresh**.
4. Use the Date Range and Region slicers at the top and the sidebar filters to explore the data.
5. Select any bar, treemap block or state to cross-filter the other visuals.

**Requirements:** Power BI Desktop (latest version recommended).

---

## 11. Project Structure

```
Asian-Paints-Executive-Dashboard/
├── Asian_Paints_Executive_Sales_Dashboard.pbix
├── data/
│   ├── DimDate.csv
│   ├── DimProduct.csv
│   ├── DimRegion.csv
│   ├── DimDealer.csv
│   ├── FactSales.csv
│   └── FactTargets.csv
├── images/
│   └── dashboard_preview.png
└── README.md
```

---

## 12. Future Scope

- Regional Performance page
- Product Analytics page
- Dealer Performance page
- Trends & Forecasting page
- Marketing campaign analysis using `MarketingCampaigns.csv`

---

## 13. Acknowledgment

I would like to thank my mentors and trainers for their guidance and feedback throughout this project. The sample dashboard shared for reference helped me understand how an executive report is structured and designed. I also thank my friends and classmates for their support and suggestions.

The dataset is synthetic and has been used for learning purposes only. The Asian Paints name and branding appear only as part of the reference design, and no real company data is shown.

---

## 14. Author

| | |
|---|---|
| **Name** | Gupta Sneha |
| **Course** | Data Analysts |
| **Email** | snehagupta47103@gmail.com |
| **LinkedIn** | linkedin.com/in/your-profile |
| **Project** | Asian Paints – Executive Sales & Performance Dashboard |
| **Date** | 13/11/2026 |
