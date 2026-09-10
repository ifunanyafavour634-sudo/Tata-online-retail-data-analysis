# Tata-online-retail-data-analysis
Data analysis and visualization of an online retail dataset using Python and Power BI, based on the Tata Data Visualization project on Forage.
# Tata Online Retail Data Analysis

## Project Overview

This project was completed as part of the Tata Data Visualization job simulation on Forage.

The project involved analyzing an online retail dataset containing transactional records from an online retail store. The analysis was focused on understanding sales performance, customer value, country-level revenue, and product demand to provide insights that could support business decision-making.

The analysis workflow consisted of:

**Raw Data → Python Data Cleaning & Analysis → Power BI Visualization → Business Insights**

Python, primarily using Pandas, was used for data cleaning, preparation, and analysis. Power BI was then used to create the final visualizations and dashboard.

---

## Business Problem

The online retail business wanted to better understand its sales performance and customer activity across different time periods, countries, and customers.

As part of the project, the CEO and CMO required answers to four business questions:

1. What was the company's monthly revenue throughout 2011, and what seasonal patterns could be observed?
2. Which 10 countries generated the highest revenue, excluding the United Kingdom, and what quantity was sold in those countries?
3. Which 10 customers generated the highest revenue?
4. Which countries had the highest demand, based on quantity sold, excluding the United Kingdom?

The analysis was designed to provide clear visual and data-driven information that could support business decisions around sales performance, customer targeting, and international expansion.

---

## Project Objectives

The main objectives of the project were to:

* Clean and prepare the retail transaction data for analysis.
* Calculate revenue from quantity sold and unit price.
* Analyze monthly revenue throughout 2011.
* Identify the top 10 countries by revenue, excluding the United Kingdom.
* Compare revenue with quantity sold for the top-performing countries.
* Identify the top 10 customers by total revenue.
* Analyze country-level demand using quantity sold.
* Create an interactive Power BI dashboard to communicate the results.
* Present business insights that can support decision-making.

---

## Dataset

The dataset used in this project is the Online Retail dataset provided as part of the Tata Data Visualization job simulation on Forage.

The original dataset contains:

* **541,909 rows**
* **8 columns**

### Dataset Columns

| Column        | Description                                  |
| ------------- | -------------------------------------------- |
| `InvoiceNo`   | Invoice number associated with a transaction |
| `StockCode`   | Product/item code                            |
| `Description` | Product description                          |
| `Quantity`    | Number of units involved in the transaction  |
| `InvoiceDate` | Date and time of the transaction             |
| `UnitPrice`   | Price per unit                               |
| `CustomerID`  | Unique customer identifier                   |
| `Country`     | Customer's country                           |

---

## Tools & Technologies

### Python

Python was used for:

* Data inspection
* Data cleaning
* Data transformation
* Revenue calculation
* Date conversion
* Grouping and aggregation
* Business-question analysis
* Exporting analysis results

The main Python library used was:

* **Pandas**

### Power BI

Power BI was used for:

* Data visualization
* Business dashboard creation
* Presenting monthly revenue
* Presenting top countries by revenue and quantity
* Presenting top customers by revenue
* Presenting country-level demand

---

# Data Cleaning & Preparation

The dataset was first loaded and examined in Python.

The cleaning process focused on preparing the transaction data for meaningful sales analysis.

## 1. Removing Returned Transactions

Negative values in the `Quantity` column represent returned transactions.

For this analysis, transactions with negative quantities were removed because the objective was to analyze sales and demand rather than returns.

```python
df_clean = df[df["Quantity"] >= 0].copy()
```

This reduced the dataset from:

**541,909 rows → 531,285 rows**

---

## 2. Removing Negative Unit Prices

Two records contained negative values in the `UnitPrice` column.

These records were removed because a negative unit price would not provide a meaningful basis for calculating sales revenue.

```python
df_clean = df_clean[df_clean["UnitPrice"] >= 0].copy()
```

---

## 3. Removing Zero Unit Prices

Transactions with a zero unit price were also removed so that the revenue calculation represented transactions with an actual positive selling price.

```python
df_clean = df_clean[df_clean["UnitPrice"] > 0].copy()
```

---

## 4. Creating the Revenue Column

A new `Revenue` column was created by multiplying the quantity sold by the unit price.

```python
df_clean["Revenue"] = df_clean["Quantity"] * df_clean["UnitPrice"]
```

This created a measure of transaction-level revenue that could then be aggregated by month, country, and customer.

---

## 5. Converting the Invoice Date

The `InvoiceDate` column was converted to a datetime format.

```python
df_clean["InvoiceDate"] = pd.to_datetime(
    df_clean["InvoiceDate"],
    format="%m/%d/%Y %H:%M"
)
```

This allowed the data to be grouped by year and month for time-series analysis.

---

# Python Analysis

## Business Question 1 — Monthly Revenue in 2011

### Objective

The CEO wanted to view revenue throughout 2011 at a monthly level in order to identify seasonal trends and support forecasting for the following year.

First, transactions from 2011 were isolated:

```python
df_2011 = df_clean[df_clean["InvoiceDate"].dt.year == 2011].copy()
```

Monthly revenue was then calculated by grouping the transactions according to the month of the invoice date.

```python
monthly_revenue = (
    df_2011.groupby(df_2011["InvoiceDate"].dt.month)["Revenue"]
    .sum()
    .reset_index()
)

monthly_revenue.columns = ["Month", "Revenue"]
```

The numeric month values were then converted into month names:

```python
monthly_revenue["Month"] = monthly_revenue["Month"].map({
    1: "January",
    2: "February",
    3: "March",
    4: "April",
    5: "May",
    6: "June",
    7: "July",
    8: "August",
    9: "September",
    10: "October",
    11: "November",
    12: "December"
})
```

The resulting monthly revenue data was used to create the 2011 revenue time-series visualization in Power BI.

---

# Business Question 2 — Top 10 Countries by Revenue

### Objective

The CMO wanted to identify the 10 countries generating the highest revenue, excluding the United Kingdom, and to compare their revenue with the quantity sold.

The United Kingdom was excluded as specifically required by the business question.

```python
df_q2 = df_clean[df_clean["Country"] != "United Kingdom"].copy()
```

Revenue was aggregated by country:

```python
country_revenue = (
    df_q2.groupby("Country")["Revenue"]
    .sum()
    .reset_index()
)
```

The countries were sorted by revenue and the top 10 were selected:

```python
top10_countries = country_revenue.sort_values(
    by="Revenue",
    ascending=False
).head(10)
```

Quantity sold was also aggregated by country:

```python
country_quantity = (
    df_q2.groupby("Country")["Quantity"]
    .sum()
    .reset_index()
)
```

The revenue and quantity results were then combined:

```python
top10_countries = top10_countries.merge(
    country_quantity,
    on="Country",
    how="left"
)
```

The resulting dataset was used in Power BI to visualize the top 10 countries by revenue alongside quantity sold.

---

# Business Question 3 — Top 10 Customers by Revenue

### Objective

The CMO wanted to identify the highest-value customers based on total revenue, with the intention of understanding which customers generated the greatest sales value.

Because this analysis required customer identification, records without a `CustomerID` were excluded.

```python
df_customer = df_clean.dropna(
    subset=["CustomerID"]
).copy()
```

Revenue was then grouped by customer:

```python
customer_revenue = (
    df_customer.groupby("CustomerID")["Revenue"]
    .sum()
    .reset_index()
)
```

The customers were sorted from highest to lowest revenue, and the top 10 were selected:

```python
top10_customers = customer_revenue.sort_values(
    by="Revenue",
    ascending=False
).head(10)
```

The resulting data was used to create a top 10 customers revenue visualization in Power BI.

---

# Business Question 4 — Country-Level Demand

### Objective

The CEO wanted to understand demand across countries in order to identify potential opportunities for international expansion.

The United Kingdom was excluded as required by the business question.

The analysis used total quantity sold as the measure of demand.

```python
country_demand = (
    df_q2.groupby("Country")["Quantity"]
    .sum()
    .reset_index()
)
```

The countries were then sorted by quantity sold:

```python
country_demand = country_demand.sort_values(
    by="Quantity",
    ascending=False
)
```

The resulting dataset was visualized in Power BI to provide a single-country-level view of demand.

---

# Exporting the Analysis Results

The four analytical datasets were exported from Python for use in the visualization stage.

```python
monthly_revenue.to_excel(
    "2011_monthly_revenue.xlsx",
    index=False
)

top10_countries.to_excel(
    "top10_countries.xlsx",
    index=False
)

top10_customers.to_excel(
    "top10_customers.xlsx",
    index=False
)

country_demand.to_excel(
    "country_demand.xlsx",
    index=False
)
```

These outputs were then used as inputs for the Power BI visualization stage.

---

# Power BI Visualization

After completing the cleaning and analysis in Python, the results were visualized using Power BI.

The dashboard contains visualizations addressing all four business questions:

### 1. 2011 Monthly Revenue

A line chart was used to display monthly revenue throughout 2011 and make changes in revenue over time easier to identify.

### 2. Top 10 Countries by Revenue

A column chart was used to compare the highest-revenue countries, excluding the United Kingdom, while also displaying quantity sold.

### 3. Top 10 Customers by Revenue

A column chart was used to rank the top 10 customers according to the revenue they generated.

### 4. Country-Level Demand

A country-level map visualization was used to display demand based on quantity sold across countries, excluding the United Kingdom.

The Power BI dashboard brings the four analyses together into a single business-focused view.

---

# Key Findings

The analysis provided several important observations:

* Revenue varied across the months of 2011, making monthly analysis useful for identifying changes in sales performance and potential seasonal patterns.
* Revenue performance differed considerably across countries when the United Kingdom was excluded.
* The highest-revenue countries were not necessarily identified solely by quantity sold, demonstrating the importance of considering both revenue and volume.
* A relatively small group of customers accounted for the highest individual customer revenue, making them important high-value customers to monitor and target.
* Country-level quantity sold revealed differences in demand across international markets and can help identify markets that may warrant further investigation for expansion.

The Power BI dashboard provides a visual way to explore these patterns and support business discussions.

---

# Business Recommendations

Based on the analysis, the business could consider:

### 1. Monitor seasonal revenue patterns

The monthly 2011 revenue trend can be used to identify periods of stronger or weaker sales and support future sales planning.

### 2. Prioritize high-performing international markets

Countries generating strong revenue should be monitored closely to understand what is driving their performance and whether additional investment could generate further growth.

### 3. Develop strategies for high-value customers

The top customers by revenue can be considered for targeted customer-retention and relationship-building strategies.

### 4. Investigate high-demand markets

Countries with high quantities sold may represent strong demand and could be investigated further when considering international expansion.

### 5. Consider revenue and volume together

Looking at both revenue and quantity sold provides a more complete view of market performance than relying on either measure alone.

---

# Project Workflow

```text
Online Retail Dataset
        ↓
Python / Pandas
        ↓
Data Cleaning
        ↓
Revenue Calculation
        ↓
Business Question Analysis
        ↓
Analysis Results
        ↓
Power BI
        ↓
Dashboard & Visualizations
        ↓
Business Insights
```

---

# Project Files

```text
tata-online-retail-data-analysis/
│
├── README.md
│
├── Python/
│   └── tata_retail_analysis.py
│
├── PowerBI/
│   └── dashboard.png
│
└── Data/
    └── README.md
```

### Python

Contains the Python analysis used for data cleaning, preparation, and business-question analysis.

### PowerBI

Contains the final dashboard screenshot.

### Data

Contains information about the dataset source rather than the original raw dataset.

---

# Skills Demonstrated

* Python
* Pandas
* Data Cleaning
* Data Preparation
* Exploratory Data Analysis
* Data Transformation
* Data Aggregation
* Business Analysis
* Revenue Analysis
* Customer Analysis
* Country-Level Analysis
* Time-Series Analysis
* Power BI
* Data Visualization
* Business Intelligence
* Data Storytelling

---

# Conclusion

This project demonstrates an end-to-end data analysis workflow, beginning with raw transactional data and progressing through data cleaning, analysis, visualization, and business interpretation.

Python was used to prepare and analyze the data, while Power BI was used to communicate the results through an interactive business dashboard.

The project provided practical experience in transforming a large retail dataset into structured information that can support decisions around revenue performance, customer value, international markets, and demand.

---

## Project Source

This project was completed as part of the **Tata Data Visualization job simulation on Forage**.
