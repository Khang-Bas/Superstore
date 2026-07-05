# 📊 Sales Performance Dashboard — Superstore 2014–2017

> **Objective:** Analyze the business performance of a US retail company, identify profitability issues, and recommend actionable improvements.

---

## 📁 Dataset

| Info | Details |
|---|---|
| **Source** | [Superstore Dataset — Kaggle](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final) |
| **Size** | 9,994 orders × 21 columns |
| **Period** | Jan 3, 2014 → Dec 30, 2017 |
| **Scope** | United States, 4 regions (West, East, South, Central) |

**Key columns:**

| Column | Description |
|---|---|
| `Order Date` | Date the order was placed |
| `Category` / `Sub-Category` | Product grouping |
| `Region` / `State` | Sales territory |
| `Sales` | Revenue (USD) |
| `Profit` | Net profit after costs (USD) |
| `Discount` | Discount rate applied |
| `Quantity` | Units sold |

---

## 🛠️ Tools & Technologies

- **Python 3.x** — data processing and analysis
- **Pandas** — data manipulation and aggregation
- **Plotly** — interactive visualization

---

## 🔄 Data Processing Pipeline

### 1. Load & Quality Check

```python
df = pd.read_csv("Sample - Superstore.csv", encoding="latin-1")
```

Initial inspection results:
- ✅ **No null values** in any column
- ✅ **No duplicate rows**
- ✅ Data types valid after parsing `Order Date` to datetime

### 2. Feature Engineering

Created new columns to support analysis:

```python
# Extract time components
df["Order Date"] = pd.to_datetime(df["Order Date"])
df["Year"]  = df["Order Date"].dt.year
df["Month"] = df["Order Date"].dt.to_period("M")

# Calculate Profit Margin %
df["Profit Margin %"] = (df["Profit"] / df["Sales"] * 100).round(2)

# Bin discount into groups
df["Discount Group"] = pd.cut(
    df["Discount"],
    bins=[-0.01, 0, 0.2, 0.4, 1.0],
    labels=["No Discount", "Low (1-20%)", "Medium (21-40%)", "High (40%+)"]
)
```

### 3. Aggregation

Summarized data across 4 analytical dimensions:

```python
monthly  = df.groupby("Month")[["Sales", "Profit"]].sum()
sub_cat  = df.groupby("Sub-Category")[["Sales", "Profit"]].sum()
region   = df.groupby("Region")[["Sales", "Profit"]].sum()
discount = df.groupby("Discount Group")[["Sales", "Profit"]].mean()
```

---

## 📊 Overall KPIs

| KPI | Value |
|---|---|
| **Total Revenue** | $2,297,201 |
| **Total Profit** | $286,397 |
| **Overall Profit Margin** | 12.47% |
| **Total Orders** | 9,994 |

---

## 🔍 4 Business Questions & Key Insights

### ❓ Q1: Is revenue and profit trending up or down over time?

![Monthly Sales & Profit Trend](q1_monthly_trend.png) (png in charts folder)

**Findings:**
- Sales shows a **clear upward trend** from 2014 → 2017, peaking at **$118,448** in November 2017
- Profit **does not grow proportionally** with Sales — the gap between the two lines widens over time
- **2 months with negative profit:** July 2014 (-$841) and January 2015 (-$3,281)
- Clear **seasonality:** Q4 (Sep–Nov) consistently peaks every year

**Business meaning:**
> The company is growing revenue but **failing to grow profit at the same rate** — a sign that costs or discounts are eating into margins. Profit Margin KPIs must be tracked alongside revenue targets.

---

### ❓ Q2: Which Sub-Categories are most and least profitable?

![Profit by Sub-Category](q2_subcategory_profit.png) (png in charts folder)

**Loss-making Sub-Categories:**

| Sub-Category | Profit | Margin % |
|---|---|---|
| Tables | -$17,725 | -8.56% |
| Bookcases | -$3,473 | -3.02% |
| Supplies | -$1,189 | -2.55% |

**Top Profitable Sub-Categories:**

| Sub-Category | Profit | Margin % |
|---|---|---|
| Copiers | +$55,618 | 37.20% |
| Phones | +$44,516 | 13.49% |
| Accessories | +$41,937 | 25.05% |

**Business meaning:**
> All three loss-making sub-categories — **Tables, Bookcases, and Supplies — belong to Furniture**. This is a structural issue, not a coincidence. Meanwhile, **Copiers delivers a 37.2% margin** — nearly 3× the company average — pointing to a strong opportunity to grow the Technology segment.

---

### ❓ Q3: Which region is performing best?

![Profit by Region](q3_region_profit.png) (png in charts folder)

| Region | Sales | Profit | Margin % |
|---|---|---|---|
| **West** | $725,458 | $108,418 | **14.94%** |
| East | $678,781 | $91,523 | 13.48% |
| South | $391,722 | $46,749 | 11.93% |
| **Central** | $501,240 | $39,706 | **7.92%** |

**Business meaning:**
> **Central is the most concerning region** — it generates $501K in sales (higher than South) but only a 7.92% profit margin, **nearly half of West's 14.94%**. The company is selling a lot in Central but keeping very little. Root cause is likely excessive discounting or higher operating costs in that region.

---

### ❓ Q4: Is discounting destroying profit?

![Profit Margin by Discount Group](q4_discount_impact.png) (png in charts folder)

| Discount Group | Profit Margin % |
|---|---|
| No Discount | **+29.51%** |
| Low (1–20%) | +11.91% |
| Medium (21–40%) | **-15.30%** |
| High (40%+) | **-77.40%** |

**Business meaning:**
> **The danger threshold is 21%** — any discount above this level results in a loss. At the 40%+ tier, the company **loses $77 for every $100 in revenue**. This is the most critical finding in the entire analysis. Sales teams may be using aggressive discounts to hit revenue KPIs without accounting for profitability.

---

## 💡 Summary: Insights & Recommendations

| # | Insight | Recommendation |
|---|---|---|
| 1 | Sales is growing but profit is not keeping pace | Track Profit Margin KPIs alongside revenue targets |
| 2 | Tables is losing -$17,725 at -8.56% margin | Review pricing strategy or consider discontinuing |
| 3 | Central region margin is only 7.92% | Investigate cause: high costs or excessive discounts in the region |
| 4 | Discounts above 40% cause -77.4% margin | Cap maximum discount at 20% company-wide |
| 5 | Copiers margin is 37.2% — 3× company average | Increase marketing investment and inventory for Copiers |

---

## 🚧 Future Improvements

**Analysis:**
- Add **Customer Segmentation** analysis (Consumer vs Corporate vs Home Office)
- Drill into Central region by State to identify the root cause
- Build a **Sales & Profit forecast** for 2018 using Time Series (ARIMA or Prophet)
- Run statistical tests to confirm discount impact on profit

**Technical:**
- Connect directly to Power BI for live dashboard updates
- Automate the data pipeline to run on a schedule
- Add a State-level map chart for geographic distribution

**Visualization:**
- Add interactive drill-down from Category → Sub-Category
- Build a customer cohort analysis view

---

## 📂 Project Structure

```
superstore-dashboard/
│
├── data/
│   └── Sample - Superstore.csv
│
├── notebooks/
│   └── analysis.ipynb
│
├── charts/
│   ├── q1_monthly_trend.png
│   ├── q2_subcategory_profit.png
│   ├── q3_region_profit.png
│   └── q4_discount_impact.png
│
├── Sales_Dashboard.html       ← Open in browser for interactive dashboard
├── requirements.txt
└── README.md
```

---

## ▶️ How to Run

```bash
# 1. Clone the repo
git clone https://github.com/your-username/superstore-dashboard.git
cd superstore-dashboard

# 2. Install dependencies
pip install -r requirements.txt

# 3. Run the notebook
jupyter notebook notebooks/analysis.ipynb

# 4. Open the dashboard
open Sales_Dashboard.html
```

---

*This project is part of a Data Analytics Portfolio. See more at [GitHub Profile](https://github.com/your-username)*
