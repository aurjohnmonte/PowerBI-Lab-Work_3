# Laboratory Work 3

## Merged Data, DAX, and DAX Function Insights

### Data Modeling Fundamentals Using Merged Data (Power BI)

---

# Part 1: Review the Merged Dataset

## Guide Questions

### 1. Is the dataset in flat-table format?

Yes. The dataset is in a **flat-table format** because customer information and transaction data are stored in a single table. This means all attributes such as customer details and sales records are combined together instead of being separated into related tables.

### 2. Which columns describe customers?

The columns that describe customers are:

* `CustomerID`
* `CustomerName`
* `City`
* `Region`

These columns contain descriptive information about customers rather than transactional data.

### 3. Which columns represent transactions?

The columns representing transactions are:

* `OrderID`
* `CustomerID`
* `OrderDate`
* `SalesAmount`

These columns describe individual sales events or business transactions.

---

# Part 2: Identify Fact and Dimension Tables

## Fact Table

### FactSales

Columns included:

* `OrderID`
* `CustomerID`
* `OrderDate`
* `SalesAmount`

The **FactSales** table stores measurable business events such as sales transactions.

---

## Dimension Table

### DimCustomer

Columns included:

* `CustomerID`
* `CustomerName`
* `City`
* `Region`

The **DimCustomer** table contains descriptive attributes about customers.

---

## Guide Questions

### 1. Why must dimension tables contain unique keys?

Dimension tables must contain **unique keys** to ensure that each record represents a single entity. This prevents ambiguity when creating relationships between fact and dimension tables.

### 2. What problems occur if duplicates exist in DimCustomer?

If duplicates exist in `DimCustomer`, several issues may occur:

* Incorrect aggregations
* Many-to-many relationships
* Data inconsistencies
* Incorrect filtering in reports

Unique values ensure a **proper one-to-many relationship** between dimension and fact tables.

---

# Part 3: Define Primary & Foreign Keys

## Table Key Structure

| Table       | Column     | Key Type    |
| ----------- | ---------- | ----------- |
| DimCustomer | CustomerID | Primary Key |
| FactSales   | OrderID    | Primary Key |
| FactSales   | CustomerID | Foreign Key |

---

## Guide Questions

### 1. What is a primary key?

A **primary key** is a column that uniquely identifies each record in a table. It ensures that every row in the table is distinct.

### 2. Why is CustomerID a foreign key in FactSales?

`CustomerID` is a **foreign key** in the FactSales table because it links each sales transaction to a specific customer stored in the DimCustomer table.

This relationship allows Power BI to connect transaction data with customer details.

---

# Part 4: Create Relationships

## Relationship Configuration

Relationship created in Model View:

```
DimCustomer[CustomerID] (1) ────< FactSales[CustomerID]
```

Configuration:

* Relationship Type: **One-to-Many**
* Cardinality: **1 : Many**
* Filter Direction: **Single**

---

## Guide Questions

### 1. Why should relationships flow from dimension to fact?

Relationships should flow from **dimension tables to fact tables** because dimensions contain descriptive data used to filter or categorize facts.

This allows users to filter sales data by attributes like customer, region, or city.

### 2. What happens if the relationship is Many-to-Many?

A **Many-to-Many relationship** can cause several problems:

* Incorrect aggregations
* Ambiguous filtering
* Slower query performance
* Difficulty in analyzing data accurately

Star schemas avoid this by enforcing **one-to-many relationships**.

---

# Part 5: Validate the Data Model

## Table Visual Fields

The following fields were added to the table visual:

* `CustomerName`
* `Region`
* `SalesAmount`

The visual confirmed that sales values correctly aggregate per customer and region.

---

## Guide Questions

### 1. Did sales aggregate correctly per customer?

Yes. Sales aggregated correctly because the relationship between **DimCustomer and FactSales** allowed Power BI to group and sum sales by customer.

### 2. What would happen if relationships were missing?

If relationships were missing:

* Customer information would not connect to sales data
* Aggregations would not work correctly
* Visuals may show incorrect or duplicated results

Relationships are essential for accurate data modeling.

---

# Enhancement Activities

## Enhancement 1: Add DimDate Table

### DAX Formula

```DAX
DimDate = CALENDAR(DATE(2025,1,1), DATE(2025,12,31))
```

Relationship created:

```
DimDate[Date] (1) ────< FactSales[OrderDate]
```

This enables **time-based analysis** such as monthly or yearly trends.

---

## Enhancement 2: Create DimRegion Table

A new dimension table can be created to store region data.

Example columns:

* `RegionID`
* `RegionName`

This allows improved filtering and hierarchical analysis.

---

## Enhancement 3: Compare Flat vs Star Schema Performance

### Flat Table Model

* Simpler structure
* Higher redundancy
* Slower performance for large datasets

### Star Schema Model

* Organized structure
* Reduced redundancy
* Faster query performance
* Better support for DAX calculations

The **star schema generally performs better** in analytical systems like Power BI.

---

# Expected Data Model Structure

```
DimCustomer (1)
      |
      |
      v
   FactSales
      ^
      |
      |
DimDate (1)
```

---

# Written Schema Explanation

The data model follows a **star schema design**. In this structure:

* **FactSales** acts as the central fact table storing transactional sales data.
* **DimCustomer** provides descriptive information about customers.
* **DimDate** enables time-based analysis.

Dimension tables filter the fact table, allowing users to analyze sales data by customer attributes and dates.

---

# Introduction to DAX

## Created Measures

### 1. Total Sales

```DAX
Total Sales = SUM(FactSales[SalesAmount])
```

### 2. Total Quantity

```DAX
Total Quantity = SUM(FactSales[Quantity])
```

### 3. Average Sales

```DAX
Average Sales = AVERAGE(FactSales[SalesAmount])
```

These measures were displayed using **Card visuals** in Power BI.

---

## Guide Questions

### 1. What is a DAX measure?

A **DAX measure** is a calculation created using Data Analysis Expressions (DAX) that dynamically computes values based on the current filter context in a report.

### 2. Difference between SUM and AVERAGE?

| Function | Description                           |
| -------- | ------------------------------------- |
| SUM      | Adds all numeric values in a column   |
| AVERAGE  | Calculates the mean value of a column |

SUM returns the total value, while AVERAGE returns the average.

### 3. Why use measures instead of calculated columns?

Measures are preferred because:

* They calculate results **dynamically**
* They consume **less memory**
* They adapt to **filters and slicers**

Calculated columns store results permanently, increasing model size.

---

# DAX Functions

## Year-to-Date Sales

```DAX
YTD Sales = TOTALYTD(SUM(FactSales[SalesAmount]), DimDate[Date])
```

This measure calculates the total sales from the **beginning of the year up to the current date**.

---

## Monthly Sales Growth

```DAX
Monthly Growth =
DIVIDE(
    [Total Sales] - CALCULATE([Total Sales], PREVIOUSMONTH(DimDate[Date])),
    CALCULATE([Total Sales], PREVIOUSMONTH(DimDate[Date]))
)
```

This measure calculates the **percentage change in sales compared to the previous month**.

---

## Guide Questions

### 1. What is time intelligence in DAX?

Time intelligence refers to DAX functions that allow calculations across **time periods** such as:

* Year-to-Date
* Month-over-Month
* Quarter-to-Date
* Year-over-Year

These functions help analyze business trends over time.

### 2. Why is a date table required?

A **date table** is required because time intelligence functions rely on a continuous and structured set of dates to correctly calculate time-based aggregations.

### 3. How does PREVIOUSMONTH help analyze trends?

`PREVIOUSMONTH` retrieves the sales value from the previous month, allowing comparison between the current and past period to measure growth or decline.

### 4. What insights can YTD Sales provide?

Year-to-Date Sales can show:

* Overall business performance for the year
* Sales progress toward annual targets
* Seasonal sales trends
* Growth compared to previous periods

---

# Learning Outcomes Achieved

✔ Understand star schema design
✔ Apply normalization concepts
✔ Build Power BI relationships
✔ Prepare data for DAX analytics

---
