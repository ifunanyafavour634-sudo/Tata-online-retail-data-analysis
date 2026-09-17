# Tata-online-retail-data-analysis
# TATA ONLINE RETAIL DATA ANALYSIS
<img width="578" height="323" alt="Screenshot 2026-09-10 012735" src="https://github.com/user-attachments/assets/9288a6f3-2329-4da5-a6fe-c7a4b74f19f4" />

## TABLE OF CONTENTS

* [BACKGROUND](#background)
* [DATA STRUCTURE](#data-structure)
* [EXECUTIVE SUMMARY](#executive-summary)
* [INSIGHTS DEEP DIVE](#insights-deep-dive)
* [RECOMMENDATIONS](#recommendations)
* [ASSUMPTIONS AND CAVEATS](#assumptions-and-caveats)
* [NEXT STEPS](#next-steps)

---

# BACKGROUND

The Tata Group is a global business group operating across more than 100 countries and six continents. This project was completed as part of the **Tata Data Visualisation: Empowering Business with Effective Insights** job simulation on Forage, which simulates the work of a data visualisation analyst at Tata Insights and Quants (Tata iQ). The simulation focuses on using data visualisation and analysis to uncover insights that can support business decision-making.

The business scenario centres on an online retail business and requires the analyst to prepare insights for senior business stakeholders, particularly the **CEO and CMO**.

As the data analyst, my objective was to transform the raw online retail transaction data into meaningful business insights by cleaning and analysing the data with **Python**, then communicating the results through an interactive **Power BI dashboard**.

The analysis was designed to answer four key questions raised from the perspective of the CEO and CMO:

1. **CEO — Monthly Revenue Trend:**
   What does the company's revenue look like month by month throughout 2011, and are there seasonal patterns that could support forecasting for the following year?

2. **CMO — Top 10 Countries:**
   Which 10 countries generated the highest revenue, excluding the United Kingdom, and how does the quantity sold compare across these markets?

3. **CMO — Top 10 Customers:**
   Who are the top 10 customers by revenue, ranked from highest to lowest, so that the business can identify its highest-value customers?

4. **CEO — Country Demand:**
   How does product demand vary across countries, excluding the United Kingdom, and which markets could represent potential opportunities for expansion?

The project followed an analytical workflow of:

**Raw Data → Data Cleaning & Preparation → Python Analysis → Business Insights → Power BI Visualization**

The official Tata simulation consists of four tasks covering business-scenario framing, choosing appropriate visuals, creating effective visuals, and communicating insights and analysis.

### Analytical Focus

The analysis focuses on four areas:

1. **Revenue Performance** — examining monthly revenue throughout 2011 to identify changes and seasonal patterns.

2. **Geographic Revenue Performance** — identifying the highest-revenue countries outside the United Kingdom and comparing their sales quantities.

3. **Customer Value** — identifying the customers contributing the greatest revenue to the business.

4. **Geographic Demand** — evaluating product demand across countries to identify markets with stronger purchasing activity.

---

# DATA STRUCTURE

## DATASET OVERVIEW

The analysis uses the **Online Retail Data Set** supplied for the Tata Data Visualisation project.

The original dataset contains **541,909 transaction records and 8 columns**.

| Column      | Description                          |
| ----------- | ------------------------------------ |
| InvoiceNo   | Unique invoice/transaction reference |
| StockCode   | Product/item code                    |
| Description | Product description                  |
| Quantity    | Number of units purchased            |
| InvoiceDate | Date and time of the transaction     |
| UnitPrice   | Price per unit                       |
| CustomerID  | Customer identifier                  |
| Country     | Customer's country                   |

The dataset contains transaction-level information covering online retail purchases from **December 2010 through December 2011**.

## DATA QUALITY AND INITIAL CHECKS

The initial inspection identified missing values in two fields:

* **Description:** 1,454 missing records
* **CustomerID:** 135,080 missing records

The remaining six fields contained no missing values in the original dataset.

The dataset also contained transactions with negative quantities. These records represent returned items and were removed from the analysis because the project analysis focuses on completed sales activity rather than returned quantities.

There were also records with negative and zero unit prices. These were excluded before revenue calculations to prevent invalid price values from affecting the analysis.

### Data Cleaning

The main cleaning steps were:

1. Removed transactions where `Quantity < 0`.

2. Removed transactions where `UnitPrice < 0`.

3. Removed transactions where `UnitPrice = 0`.

4. Created a new `Revenue` field using:

   **Revenue = Quantity × UnitPrice**

5. Converted `InvoiceDate` into a datetime field for time-based analysis.

6. For customer-level analysis, records without a `CustomerID` were excluded because they could not be reliably attributed to an individual customer.

7. For the country-specific analyses required by the business brief, the **United Kingdom was excluded**.

After removing negative quantities and invalid price records, the working dataset contained **530,104 records**.

### Data Preparation in Python

The analysis was performed using Python and Pandas. The cleaned dataset was then aggregated according to the four business questions before the resulting datasets were used for Power BI visualization.

The principal derived metric was:

```python
df_clean["Revenue"] = df_clean["Quantity"] * df_clean["UnitPrice"]
```

For customer-level analysis, records with missing customer identifiers were excluded:

```python
df_customer = df_clean.dropna(subset=["CustomerID"]).copy()
```

For the country-specific analyses:

```python
df_q2 = df_clean[df_clean["Country"] != "United Kingdom"].copy()
```

The analysis therefore separates **data preparation decisions** from the subsequent business analysis, ensuring that the Power BI visuals are based on prepared and aggregated analytical outputs rather than the unprocessed raw transaction data.

---

# EXECUTIVE SUMMARY

The analysis of the online retail transaction dataset identified clear differences in revenue performance across months, countries and customers.

Revenue during 2011 increased substantially toward the final quarter, with **November recording the highest monthly revenue at approximately £1.51 million**. October generated approximately **£1.15 million**, while September generated approximately **£1.06 million**, indicating a strong upward movement in revenue during the latter part of the year.

Outside the United Kingdom, **the Netherlands generated the highest revenue at approximately £285,446**, followed closely by **EIRE at approximately £283,454**, Germany at approximately £228,867 and France at approximately £209,715. The analysis also shows that the ranking of countries by revenue does not necessarily correspond exactly to their ranking by quantity sold.

Customer-level analysis identified **Customer 14646** as the highest-revenue customer, generating approximately **£280,206**, followed by Customer 18102 at approximately **£259,657** and Customer 17450 at approximately **£194,551**.

The country-demand analysis also showed that the Netherlands had the highest quantity sold outside the United Kingdom, with **200,361 units**, followed by EIRE with **147,173 units**, Germany with **119,261 units**, and France with **112,103 units**.

These results highlight the importance of monitoring seasonal revenue patterns, protecting high-value customers, understanding differences between revenue and sales volume across international markets, and evaluating high-demand countries as potential areas for further commercial attention.

---

# INSIGHTS DEEP DIVE

## 1. REVENUE SEASONALITY — 2011

The monthly revenue analysis was developed to help the CEO understand how revenue changed throughout 2011 and whether identifiable seasonal patterns could support future planning.

Revenue fluctuated throughout the first eight months of the year before increasing strongly from September onwards.

The highest monthly revenue occurred in **November, at approximately £1.51 million**.

The strongest months were:

| Month     |       Revenue |
| --------- | ------------: |
| November  | £1,509,496.33 |
| October   | £1,154,979.30 |
| September | £1,058,590.17 |
| May       |   £770,536.02 |
| June      |   £761,739.90 |

January recorded approximately **£691,364.56**, while December recorded approximately **£638,792.68**.

The sharp increase from September through November indicates that the business experienced its strongest revenue performance toward the end of the year. This pattern is particularly relevant for inventory planning, marketing activity and operational preparation ahead of the final quarter.

## 2. TOP 10 COUNTRIES BY REVENUE

To address the CMO's requirement, the United Kingdom was excluded and countries were ranked according to total revenue.

The highest-revenue markets were:

| Rank | Country     |     Revenue | Quantity |
| ---: | ----------- | ----------: | -------: |
|    1 | Netherlands | £285,446.34 |  200,361 |
|    2 | EIRE        | £283,453.96 |  147,173 |
|    3 | Germany     | £228,867.14 |  119,261 |
|    4 | France      | £209,715.11 |  112,103 |
|    5 | Australia   | £138,521.31 |   83,901 |
|    6 | Spain       |  £61,577.11 |   27,940 |
|    7 | Switzerland |  £57,089.90 |   30,629 |
|    8 | Belgium     |  £41,196.34 |   23,237 |
|    9 | Sweden      |  £38,378.33 |   36,083 |
|   10 | Japan       |  £37,416.37 |   26,016 |

The Netherlands generated the highest revenue outside the United Kingdom, while also recording the highest quantity sold among the countries in the analysis.

The results demonstrate why the CMO should evaluate both **revenue and quantity**, rather than relying on revenue alone. A market generating high transaction volume may have different commercial characteristics from a market generating high revenue with comparatively lower volume.

## 3. TOP 10 CUSTOMERS BY REVENUE

Customer revenue was calculated after excluding records without a CustomerID. Customers were then ranked from highest to lowest according to their total revenue contribution.

The top three customers were:

| Rank | Customer ID |     Revenue |
| ---: | ----------: | ----------: |
|    1 |       14646 | £280,206.02 |
|    2 |       18102 | £259,657.30 |
|    3 |       17450 | £194,550.79 |

The remaining customers in the top 10 were:

| Rank | Customer ID |     Revenue |
| ---: | ----------: | ----------: |
|    4 |       16446 | £168,472.50 |
|    5 |       14911 | £143,825.06 |
|    6 |       12415 | £124,914.53 |
|    7 |       14156 | £117,379.63 |
|    8 |       17511 |  £91,062.38 |
|    9 |       16029 |  £81,024.84 |
|   10 |       12346 |  £77,183.60 |

The concentration of revenue among a relatively small group of high-value customers highlights the importance of understanding customer contribution when developing retention and relationship-management strategies.

## 4. COUNTRY DEMAND

Country demand was evaluated using total quantity sold, with the United Kingdom excluded as required by the business question.

The highest-demand countries were:

| Rank | Country     | Quantity Sold |
| ---: | ----------- | ------------: |
|    1 | Netherlands |       200,361 |
|    2 | EIRE        |       147,173 |
|    3 | Germany     |       119,261 |
|    4 | France      |       112,103 |
|    5 | Australia   |        83,901 |
|    6 | Sweden      |        36,083 |
|    7 | Switzerland |        30,629 |
|    8 | Spain       |        27,940 |
|    9 | Japan       |        26,016 |
|   10 | Belgium     |        23,237 |

The concentration of demand in a number of European markets, particularly the Netherlands, EIRE, Germany and France, provides useful direction for evaluating international market opportunities.

The Power BI dashboard was designed to present country-level demand in a single visual view, allowing the CEO to assess the geographical distribution of demand without relying on a long scrolling table.

---

# RECOMMENDATIONS

Based on the analysis, I would recommend the following actions:

### 1. Prepare for stronger fourth-quarter demand

The substantial increase in revenue from September through November suggests that the business should prepare inventory, fulfilment capacity and marketing activities ahead of the final quarter.

Historical monthly revenue should be incorporated into future demand and inventory planning.

### 2. Prioritize high-value international markets

The Netherlands, EIRE, Germany and France generated the strongest revenue outside the United Kingdom.

Management should evaluate these markets further to understand customer acquisition, product preferences, repeat purchasing behaviour and opportunities for controlled expansion.

### 3. Develop a high-value customer retention strategy

The top customers contribute substantial revenue and should receive focused relationship management.

Customer segmentation could be used to identify high-value customers, repeat customers and customers whose purchasing activity is declining.

### 4. Evaluate revenue alongside quantity

Management should avoid evaluating market performance using sales volume alone.

Combining revenue and quantity provides a better understanding of market value and can help distinguish high-volume markets from markets generating stronger monetary returns.

### 5. Use geographic demand to support expansion decisions

Countries with consistently high demand should be evaluated using additional commercial factors before expansion decisions are made, including customer growth, order frequency, profitability, logistics costs and operational feasibility.

---

# ASSUMPTIONS AND CAVEATS

1. **Returns:** Negative quantities were treated as returned items and removed from the sales analysis rather than being matched back to their original transactions.

2. **Invalid prices:** Negative and zero unit-price records were removed before revenue calculations to prevent invalid price values from affecting the analysis.

3. **Customer identification:** Customer-level analysis excludes records where `CustomerID` is missing because those transactions cannot be reliably attributed to an individual customer.

4. **United Kingdom exclusion:** The United Kingdom was excluded from the country-level analyses where the Tata business question specifically required it.

5. **Revenue calculation:** Revenue was calculated as `Quantity × UnitPrice` based on the transaction-level fields available in the dataset.

6. **Descriptive analysis:** The analysis identifies historical patterns in the available transaction data. The observed monthly pattern should therefore be treated as evidence for planning rather than a guarantee of future performance.

7. **Expansion decisions:** Country demand and revenue are indicators of market opportunity, but they should be combined with profitability, customer growth, logistics and other commercial considerations before investment decisions are made.

---

# NEXT STEPS

1. Extend the analysis beyond 2011 when additional transaction data becomes available to determine whether the observed seasonal patterns remain consistent.

2. Introduce customer segmentation to distinguish high-value, frequent and declining customers.

3. Analyse product-level performance to identify the products contributing most to revenue and demand in each major market.

4. Incorporate profitability and other operational costs where available so that market opportunities can be evaluated beyond revenue and quantity.

5. Develop a recurring Power BI reporting process that allows management to monitor revenue, customer contribution and geographic demand over time.

6. Use the findings as a starting point for deeper market and customer analysis before making expansion or retention decisions.

---

## PROJECT WORKFLOW

**Raw Online Retail Data → Python Data Cleaning → Python Business Analysis → Aggregated Analysis Outputs → Power BI Dashboard → Business Insights → Recommendations**

## TOOLS USED

* **Python**

  * Pandas
  * Data cleaning
  * Data transformation
  * Aggregation
  * Business analysis

* **Power BI**

  * Data visualization
  * Dashboard development
  * KPI presentation
  * Interactive filtering
  * Geographic visualization

## PROJECT OUTCOME

The project transformed a raw dataset containing **541,909 retail transactions** into a structured analytical workflow that answers four executive-level business questions around revenue seasonality, international markets, customer value and geographic demand.

The analysis demonstrates the use of data cleaning, exploratory analysis, aggregation and business-focused visualization to translate transactional data into insights that can support management decision-making.
   
