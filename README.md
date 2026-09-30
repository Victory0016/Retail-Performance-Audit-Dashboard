# Retail Performance Dashboard —Auditing a Flawed Management Dashboard 
## Dashboard Preview
<img width="701" height="338" alt="Screenshot 2026-09-30 170215" src="https://github.com/user-attachments/assets/53eed367-6e2d-42b9-ba66-6c575d2a15c0" />

**Built in Excel using PivotTables, PivotCharts and a slicer for State-level filtering. Palette inspired by Jumia's brand colors (orange navy and white) to give the dashboard a recognisable, retail-native feel.**

**A data quality audit and corrected retail dashboard, built for the DSN (Data Science Nigeria) AI Bootcamp 2026  Data Analytics Track.**

I was given a retail dataset, a dashboard that had already been shown to management, and a set of business conclusions drawn from it. The brief: don't reproduce the dashboard,audit it. Decide what could be trusted, correct what couldn't, and tell management what actually happened in their business.

Four of the four visible conclusions on the original dashboard turned out to be wrong or misleading. This project documents how I found that out, and what the real numbers say instead.

---

## Table of Contents

1. [Dashboard Preview](#dashboard-preview)
2. [Project Background](#project-background)
3. [Tools Used](#tools-used)
4. [About the Dataset](#about-the-dataset)
5. [Data Cleaning Process](#data-cleaning-process)
6. [Auditing the Management Claims](#auditing-the-management-claims)
7. [Key Insights](#key-insights)
8. [Dashboard Walkthrough](#dashboard-walkthrough)
9. [Recommendations to Management](#recommendations-to-management)
10. [What I'd Do Differently / Next Steps](#what-id-do-differently--next-steps)
11. [Repository Structure](#repository-structure)
12. [About Me](#about-me)

---

## Project Background

I took part in the Data Science Nigeria (DSN) AI Bootcamp 2026, under the Data Analytics Track. As part of the final project, I was handed a dataset and a scenario: I'd just joined a retail business as their Data Analyst, and management had already been shown a dashboard built from that same dataset, along with a set of conclusions they were ready to act on.

My job wasn't to rebuild what was already there. It was to question it.

So before I trusted a single number, I went through the raw dataset line by line 432 transactions across five Nigerian states, three sales channels, and nine products. What I found wasn't a clean dataset with a few rounding errors. It was duplicate transactions, inconsistent labels, missing costs quietly treated as zero, and a "strongest market" conclusion that fell apart the moment the data was fixed.

The dashboard management had already seen made four specific claims:
1. Abuja is the most profitable market.
2. Electronics is the strongest category and should receive more budget.
3. Higher discounting is improving profitability.
4. Online has the lowest return rate.

I treated each one the way I'd treat a claim from a client in the real world  not as something to confirm, but as something to test. I cleaned the data, recalculated every figure from scratch, and checked each claim against the evidence.

**None of the four survived.**

This project is the record of that process  what I found wrong, how I fixed it, and what the numbers actually say once they can be trusted.

---

## Tools Used

| Tool | Purpose |
|---|---|
| **Microsoft Excel** | Data cleaning, PivotTables, formulas, dashboard build |
| **Excel PivotCharts** | All dashboard visuals |
| **Excel Slicers** | Interactive State-level filtering |

---

## About the Dataset

- **432 raw transaction rows**, retail sales across 5 Nigerian states, 3 sales channels, 3 product categories, and 9 products
- **16 original columns**: Transaction ID, Date, Store ID, State, Channel, Product Category, Product, Customer ID, Customer Type, Units, Unit Price, Discount %, Reported Revenue, Cost Per Unit, Return Flag, Sales Rep
- Time range: **1 January – 26 August 2026** (the final month is partial)

---

## Data Cleaning Process

I treated the raw file the way I would a live client handover, verify before trusting anything.

### 1. Duplicate transactions
12 rows were exact duplicates of earlier rows same Transaction ID, identical in every field sitting at the bottom of the file. They inflated reported revenue by ₦1,130,900 and profit by ₦379,600, and double-counted 2 returns.
**Action:** removed, keeping the first occurrence. 432 rows → **420 clean rows**.

### 2. Inconsistent text labels
- State: `Lagos`, `Lagos ` (trailing space), `LAGOS`  three labels for one market.
- Category: `Accessories`, `Accessories `, `accessories`, `Accessory` four labels for one category.

This had fragmented Lagos across 3 separate dashboard rows and split Accessories into 4 separate bars, understating both.
**Action:** trimmed whitespace, standardized capitalization, and merged the variants confirmed correct using Store ID (`LG-01`/`LG-02`) and product mapping (Backpack, Phone Case, USB Cable → Accessories).

### 3. Missing cost values treated as zero
31 rows had a blank `Cost_Per_Unit`, and the original dashboard's profit calculation treated that blank as **₦0 cost** meaning those rows showed 100% profit. **20 of the 31** blank rows belonged to Abuja alone.
**Action:** filled each blank using that product's standard cost (every product has exactly one fixed cost across the whole dataset, verified before filling, so this is exact, not estimated).
**Impact:** removed ₦1,957,000 of phantom profit, ₦1,510,200 of it from Abuja — the single biggest correction in the whole project.

### 4. Revenue that didn't match its own inputs
22 rows (5.2%) had a `Revenue_Reported` value that didn't equal `Units × Unit Price × (1 − Discount)`. The typed total, not the inputs, was the unreliable field.
**Action:** recalculated revenue from Units, Price and Discount for every row and used that as the source of truth throughout.

### 5. Extreme unit quantities
9 rows had 18–25 units, while every other row had 1–5. Rather than delete unverified data, I kept these rows (their revenue was internally consistent) but flagged them and re-ran every conclusion with and without them. No verdict changed either way.

### 6. A partial final month
Data stopped on 26 August, not the 31st. I labelled August as partial everywhere it appears and compared months using revenue-per-day as well as totals, so the comparison stayed fair.

### 7. An unreliable "Customer Type" field
102 of 241 repeat purchases by the same Customer ID were labelled "New." Since a customer can't be new twice, I excluded this field from the analysis entirely rather than build conclusions on it.

---

## Auditing the Management Claims

| # | Management's claim | My verdict | What the clean data actually shows |
|---|---|---|---|
| 1 | Abuja is the most profitable market | ❌ **Incorrect** | **Lagos** leads with ₦4,579,900 gross profit. Abuja is **last of 5 markets** at ₦1,698,100  its "top" position was built on ₦1.51M of phantom profit from blank-cost rows. |
| 2 | Electronics is the strongest category and should get more budget | ⚠️ **Misleading** | Electronics does lead on revenue (₦29.3M, 62.9%) but also carries the *highest* return rate (11.9%) and a *lower* margin (30.6%) than Accessories (46.8%). Strong on volume, not on quality — and there's no ROI data to justify "more budget." |
| 3 | Higher discounting is improving profitability | ❌ **Incorrect** | Margin falls steadily from 35.7% (0% discount) to 15.3% (25% discount), with **no increase in units sold per transaction**. Discounting is pure margin erosion here, not a growth lever. |
| 4 | Online has the lowest return rate | ❌ **Incorrect** | **In-store** is actually lowest (6.4%), followed by Online (9.7%) and WhatsApp (11.9%). The claim even contradicted the original dashboard's own table. |
| 5 | August is the strongest sales month | ❌ **Incorrect** | August is the **weakest** month (₦3.17M) but it's also a partial month (26 of 31 days), so the comparison was never fair to begin with. |
| 6 | Lagos is underperforming | ❌ **Incorrect** | Lagos is the **top-performing market** on revenue and profit. It only looked weak because its data was split across 3 inconsistent labels. |
| 7 | Reported Revenue can be used directly | ❌ **Incorrect** | Reported revenue (₦47.5M) overstated the true figure (₦46.68M) by ₦821,145  a mix of duplicate rows and 22 miscalculated values. |
| 8 | The dashboard is reliable enough to act on as-is | ❌ **Incorrect** | Every visible conclusion above failed once the data was cleaned. Profit alone was overstated by **13.9%**. |

Click to access the word document completed Answer & Submission Sheet https://docs.google.com/document/d/18oi2jgewxXgG4zOSkpeO_Yuw1Xj3Gthg/edit?usp=drive_link&ouid=116062210058453971491&rtpof=true&sd=true
---

## Key Insights

- **The most expensive mistake in the original dashboard was invisible.** Treating a blank cost field as ₦0 doesn't look like an error on a chart ,it just quietly inflates one number. It took line-by-line data validation, not a glance at the dashboard, to catch it.
- **Discounting in this business is a cost, not a growth strategy.** Every one of the deepest discounts (20–25%) came from a single market (Abuja), and none of them brought in extra units they just gave away margin the business didn't need to give away.
- **"Biggest" and "best" are not the same thing.** Electronics is the largest category by revenue, but Accessories is the more profitable one per naira of sales. A dashboard that only shows size, without margin or returns next to it, will always point decision-makers toward volume over profitability.
- **A dashboard's charts can be correct in principle and still lie.** The original "Return Rate by Channel" chart plotted raw values in the tens of millions instead of percentages a chart-type mistake sitting on top of a data-quality mistake.

---

## Dashboard Walkthrough

**KPI row:** Total Revenue, Total Cost, Gross Profit, Gross Margin, Total Transactions, Returned Transactions, Return Rate, and Average Discount  the full financial picture at a glance.

**Gross Profit by State:** the headline chart the one that overturns the Abuja claim.

**Top 5 Gross Profit by Product:** shows how concentrated the business is around Smartphone as a single product.

**Revenue by Category:** Electronics vs Home vs Accessories, set up to be read alongside the margin figures in the write-up rather than on the chart itself.

**Return Rate by Channel:** deliberately built as a bar chart, not a pie or donut  these are three independent rates with different denominators (In-store's 202 transactions, Online's 134, WhatsApp's 84), so they don't sum to a meaningful "whole." A pie chart would visually misrepresent them, which is exactly the kind of chart-choice error I was auditing the original dashboard for.

**Monthly Revenue Trend:** January through August, with August marked as a partial month so it isn't read as a real decline.

**State slicer:** lets a viewer isolate a single market and watch every chart on the dashboard respond  useful for proving the Lagos-vs-Abuja finding interactively rather than just stating it.

---

## Recommendations to Management

1. **Reset the discount policy, starting in Abuja.** Cap discounts (e.g. 15%, with approval required above 10%) and eliminate the 20–25% tier entirely  it exists only in Abuja and is actively destroying margin there.
2. **Address returns in Electronics especially the WhatsApp channel and one specific store (AB-01, 26% return rate)** rather than simply expanding Electronics' budget.
3. **Fix the data pipeline before the next dashboard is built**: enforce a unique Transaction_ID, use dropdowns for State and Category instead of free text, make Cost a required field, and calculate Revenue rather than allowing it to be typed in manually.

---

## Repository Structure

```
├── README.md
├── assets/
│   └── dashboard-preview.png
├── data/
│   ├── raw_dataset.xlsx
│   └── cleaned_dataset.xlsx
├── dashboard/
│   └── Retail_Performance_Dashboard.xlsx
└── docs/
    ├── Project_Brief.pdf
    └── Answer_and_Submission_Sheet.docx
```

---

## About Me

**Nnajekwu Chinemerem Victory**
Data Analyst | Lead Instructor & Co-Founder, Analytics Hub Africa

I work across Excel, SQL, AI Tools and Power BI, and I teach data analytics through Analytics Hub Africa. This project reflects how I approach any dataset I'm handed: verify before trusting, show the evidence behind every number, and never present a conclusion I can't defend line by line.




