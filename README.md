📌 Project Description

This project analyzes supply chain data to understand order flow, shipping performance, and operational efficiency across different regions and markets. The raw data is cleaned and reshaped in Power Query, modeled as a star schema, enriched with DAX measures, and presented in an interactive multi-page Power BI report.

🎯 Objectives
Analyze and understand supply chain operations
Evaluate delivery performance and delays
Extract insights to improve efficiency and reduce costs
Identify top-performing regions and markets
Support data-driven decision making

🗂️ Dataset
Property	Value
Raw rows (order items)	180,519
Unique orders	65,752
Unique customers	15,184
Date range	Jan 2015 – Jan 2018
Markets	Africa · Europe · LATAM · Pacific Asia · USCA (164 countries)
Product categories	118 products across 11 departments
Key categorical fields

Delivery Status: Advance shipping · Shipping on time · Late delivery · Shipping canceled
Shipping Mode: Standard Class · Second Class · First Class · Same Day
Customer Segment: Consumer · Corporate · Home Office
Payment Type: CASH · DEBIT · PAYMENT · TRANSFER
Order Status: COMPLETE · CLOSED · PENDING · PENDING_PAYMENT · PROCESSING · ON_HOLD · PAYMENT_REVIEW · SUSPECTED_FRAUD · CANCELED

🛠️ Tools & Technologies
Tool	Usage
Microsoft Excel	Raw data source
Power Query	Data cleaning, dimension building, surrogate keys
Power BI Desktop	Data modeling & interactive dashboards
DAX	KPIs, year-over-year comparisons, dynamic cards & conditional colors

Data Preparation (Power Query)

The single wide Excel table was split into a Fact table and dimension tables:

Loaded the source with Excel.Workbook and applied explicit data types to all columns.
Dropped columns not needed for analysis (e.g. shipping date, order state/region, discount rate, profit ratio).
Created each dimension by selecting its columns, removing duplicates (Table.Distinct), and adding an index column as a surrogate key (Product ID, Customer ID, Geography ID, ...).
Merged each dimension back into the fact table (Table.NestedJoin, Left Outer), expanded the key, and removed the descriptive text columns.
Kept only numeric measures, dates, and foreign keys in the fact table.
🧩 Data Model

The model is a star schema: one fact table connected to 8 dimension tables plus a date table. All relationships are many-to-one with single-direction filtering.

order date
Product ID
Customer ID
Geography ID
Department ID
Delivery status ID
Shipping Mode ID
Type ID
Order Status ID
DimDate
FactTable
DimProduct
DimCustomer
DimGeography
DimDepartment
DimDeliveryStatus
DimShippingMode
DimPaymentType
DimOrderStatus
Table	Type	Main columns
Fact Table	Fact	Order Id, Order Item Id, order date, Sales, Order Item Quantity, Order Item Total, Order Item Discount, Order Item Product Price, Order Profit Per Order, Product Price + foreign keys
Dim product	Dimension	Product ID, Product Name, Category Name, Product Image
Dim customer	Dimension	Customer ID, Customer Name, Customer Segment
Dim Geography	Dimension	Geography ID, Market, Order Country
Dim Department	Dimension	Department ID, Department Name
Dim Delivery status	Dimension	Delivery status ID, Delivery Status
Dim shiping mode	Dimension	Shipping Mode ID, Shipping Mode
Dim Payment Type	Dimension	Type ID, Type
Dim Order status	Dimension	Order Status ID, Order Status
DimDate	Date	Date, Year, Month, Monthnum, YearMonth, Weekday, Weeknum, Qtr
measures (2)	Measures	All DAX measures
📐 DAX Measures
Core KPIs
DAX
Total Sales    = SUM('Fact Table'[Sales])
Total Orders   = DISTINCTCOUNT('Fact Table'[Order Id])
Total Profit   = SUM('Fact Table'[Order Profit Per Order])
Total Quantity = SUM('Fact Table'[Order Item Quantity])
Total customer = DISTINCTCOUNT('Fact Table'[Customer ID])
Profit Margin  = DIVIDE([Total Profit], [Total Sales], 0)
AOV            = DIVIDE([Total Sales], [Total Orders], 0)
Year-over-Year pattern

The same three-measure pattern is repeated for Sales, Orders, Customers, Quantity, Profit, and Profit Margin:

DAX
Sales PV_Y   = CALCULATE([Total Sales], PREVIOUSYEAR(DimDate[Date]))
Sales Growth = DIVIDE([Total Sales] - [Sales PV_Y], [Sales PV_Y])
Sales Color  = IF([Sales Growth] > 0, "Green", "Red")
KPI	Previous-year	Growth	Color
Sales	Sales PV_Y	Sales Growth	Sales Color
Orders	Orders PV_Y	Orders Growth	Orders Color
Customers	Customers PV_Y	Customers Growth	Customers Color
Quantity	Quantity PV_Y	Qty Growth	Qty Color
Profit	Profit PV_Y	Profit Growth	Profit Color
Profit Margin	Profit Margin PV_Y	Profit Margin Growth	Profit Margin Color
Dynamic "best / worst" cards

Best Month, Top Category, and Lowest Category use TOPN + SUMMARIZE + ALLSELECTED so the cards react to the active slicers, returning the name and its sales value, for example:

DAX
Top Category =
VAR T =
    TOPN(
        1,
        SUMMARIZE(
            ALLSELECTED('Dim product'[Category Name]),
            'Dim product'[Category Name],
            "SalesValue", [Total Sales]
        ),
        [SalesValue], DESC
    )
VAR CatName     = CONCATENATEX(T, 'Dim product'[Category Name])
VAR SalesAmount = MAXX(T, [SalesValue])
RETURN
    CatName & " - " & FORMAT(SalesAmount, "#,##0")
📊 Dashboard Pages
1. Intro

Project description, objectives, team members, and a page navigator to move between report pages.

2. Overview

High-level performance of the whole supply chain.

KPI cards: Total Customers, Total Orders, Total Profit, Total Quantity, Total Sales (with year-over-year indicators)
Profit vs Orders by Month — combo chart (columns + line)
Sales & Profit by Year — line chart
Sales by Category — bar chart
Orders by Delivery Status — donut chart
Year slicer + navigation buttons
3. Sales

Deep dive into sales performance.

Top 10 Products by Sales (with Profit) — clustered bar chart
World Sales Overview — map of sales by Market
Sales by Customer Segment — donut chart
KPI cards: Average Order Value, Profit Margin, Highest Monthly Sales, Lowest Month With Sales, Best Category
Year slicer + navigation buttons

🚧 The report file also contains Customers, Orders, and an extra page that are placeholders for future work (see Roadmap).

📈 Headline Numbers

Totals across the full dataset (all years, no filters):

Metric	Value
Total Sales	≈ 36.78 M
Total Profit	≈ 3.97 M
Profit Margin	≈ 10.8 %
Total Orders	65,752
Average Order Value	≈ 559
Total Customers	15,184
Total Quantity Sold	384,079 units
🚀 How to Run
Clone or download this repository.
Open the .pbix file with Power BI Desktop (free, Windows).
The report was built against a local file path (D:\DataCoSupplyChainDataset.xlsx). To refresh the data:
Go to Home → Transform data → Data source settings
Click Change Source… and point it to your local copy of DataCoSupplyChainDataset.xlsx
Click Refresh to reload all tables.
🗺️ Roadmap
 Complete the Customers page (segments, top customers, growth)
 Complete the Orders page (order status, payment type, shipping mode, late-delivery analysis)
 Add delivery-delay analysis by region and shipping mode
 Add dashboard screenshots to images/
👥 Team
#	Member
1	Ziad Ahmed
2	Seif Mohamed
3	Mohamed Naeem
4	Marwan Hamdy
5	Nada Yousef
