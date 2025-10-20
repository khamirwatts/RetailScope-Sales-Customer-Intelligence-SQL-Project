# 🛍️ Retail Sales Analysis: SQL-Driven Business Intelligence

## Project Overview

**Project Title**: Retail Sales Analysis  
**Level**: Beginner  
**Database**: `p1_retail_db`
**SQL Dialect**: PostgreSQL

This project demonstrates end-to-end SQL analytics capabilities by building a retail sales intelligence system from scratch. It showcases data cleaning, exploratory analysis, and business-critical queries that drive actionable insights for retail operations, customer segmentation, and revenue optimization.

**Key Technologies**: PostgreSQL, SQL (CTEs, Window Functions, Date/Time Manipulation, Aggregations)

## 📊 Dataset Overview
**Source:** Zero Analyst Youtube Tutorial
**Size:** 2,000 transactions(after data cleaning)
**Time period:** 2022-2023
**Scope:** Multi-category retail transactions with customer demographics and temporal patterns
**Categories:** Clothing, Beauty, Electronics

**Schema Design:**
transactions_id (PK) | sale_date | sale_time | customer_id | gender | age | 
category | quantity | price_per_unit | cogs | total_sale

## 🎯Project Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## 🔧 Project Structure

### 1. Database Setup

- **Database Creation**: Created a PostgreSQL database with optimized table structure for retail transaction analysis named `p1_retail_db`. :

```sql
CREATE DATABASE p1_retail_db;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Cleaning & Validation:
Implemented rigorous data quality checks to ensure analytical integrity:

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```
**Data Quality Metrics**:
✅ Zero null values post-cleaning
✅ 2,000 validated transactions
✅ Complete customer demographic coverage


### 3. 📈 Business Analysis & SQL Solutions

The following SQL queries were developed to answer specific business questions:

Q1: Daily Transaction Retrieval
Business Question: Pull all sales from a specific date for daily reconciliation

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```
### 📸 Sample Results: Category Performance
<img width="440" height="108" alt="image" src="https://github.com/user-attachments/assets/24f21f53-1a2d-4e4b-a97e-d2ceea1b9cce" />

*Electronics leads in revenue despite fewer orders, indicating higher average transaction value.*


Q2: High-Volume Category Analysis
Business Question: Identify bulk clothing purchases in November 2022

```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
AND  TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
AND quantity >=4
```

Q3: Category Performance Metrics
Business Question: Calculate total revenue and order volume by product category

```sql
SELECT category, SUM(total_sale) AS total_sales_by_category,
COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category
```

Q4: Customer Demographic Profiling
Business Question: Determine average age of Beauty category customers for targeted marketing

```sql
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty'
```

Q5: Premium Transaction Identification
Business Question: Flag high-value transactions for VIP customer analysis

```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000 
```
**Result: 306 premium transactions identified (15.3% of total)**

Q6: Gender-Category Purchase Patterns
Business Question: Analyze purchase behavior across gender segments by category

```sql
SELECT category, gender, COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category
```

Q7: Seasonal Trend Analysis (Advanced)
Business Question: Identify peak sales months using window functions

```sql
SELECT 
       year,
       month,
    avg_sale
FROM 
(    
SELECT 
    EXTRACT(YEAR FROM sale_date) as year,
    EXTRACT(MONTH FROM sale_date) as month,
    AVG(total_sale) as avg_sale,
    RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales
GROUP BY 1, 2
) as t1
WHERE rank = 1
```
**Results:

2022: July (Avg: $541)
2023: February (Avg: $536)**

Q8: Top Customer Identification
Business Question: Identify highest-value customers for loyalty programs

```sql
SELECT customer_id, SUM(total_sale) AS total_sales, COUNT(total_sale) AS transactions
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5
```
### 📸 Sample Results: Top 5 Customers by Revenue
<img width="355" height="162" alt="image" src="https://github.com/user-attachments/assets/5f48f13d-67b3-441e-995c-eaa75a0f6ae9" />

*These high-value customers represent significant revenue concentration - customer 3 alone contributed over $38K across 76 transactions, making them prime candidates for VIP loyalty programs.*

Q9: Customer Reach by Category
Business Question: Measure unique customer engagement per product line

```sql
SELECT 
	category,
	COUNT(DISTINCT customer_id) AS unique_customer_count
FROM retail_sales
GROUP BY category
```

Q10: Shift-Based Demand Analysis (CTE Implementation)
Business Question: Optimize staffing by analyzing order volume across day parts

```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
```

## 🔍 Key Findings & Business Insights

Revenue Intelligence

Premium Segment: 306 transactions exceeded $1,000 (15.3% of total volume), representing high-value customer segment
Peak Seasons: July 2022 and February 2023 showed highest average sales, suggesting seasonal promotional opportunities

Customer Behavior

Multi-category shoppers: Analysis reveals overlap in customer purchase patterns across product lines
Demographic targeting: - Beauty category average customer age: 34.5 years (vs. 38.2 overall average)
- 42% of customers purchased from multiple categories

Operational Efficiency

Shift patterns: Order distribution analysis enables data-driven staffing decisions
Category performance: Revenue breakdown by category informs inventory allocation strategies

Strategic Recommendations

VIP Program: Target 306 high-value customers with personalized loyalty incentives
Seasonal Campaigns: Amplify marketing spend in July and February based on historical peaks
Staffing Optimization: Align workforce scheduling with shift-based demand patterns
Category Investment: Prioritize inventory and shelf space for top-performing product lines

## 💡 SQL Techniques Demonstrated

✅ Database Design: Schema creation and table structuring
✅ Data Cleaning: NULL handling and data validation
✅ Aggregations: SUM, AVG, COUNT with GROUP BY
✅ Window Functions: RANK() with PARTITION BY for ranking analysis
✅ CTEs: Common Table Expressions for complex query organization
✅ Date/Time Functions: EXTRACT, TO_CHAR for temporal analysis
✅ Filtering: Multi-condition WHERE clauses
✅ Subqueries: Nested queries for advanced analytics

## 🚀 Project Impact
This project transforms raw transactional data into strategic business intelligence:

For Retail Managers: Identify peak sales periods and optimize inventory
For Marketing Teams: Segment customers by demographics and purchase behavior
For Operations: Data-driven staffing and resource allocation
For Finance: Track high-value transactions and revenue trends

Real-World Application: The analytical framework built here mirrors enterprise-level retail analytics used by companies like Target, Walmart, and Amazon for business decision-making.

## 🚧 Challenges & Solutions 
**Date Manipulation & Type Conversion**
- Struggled with filtering November 2022 transactions - learned that `TO_CHAR(sale_date, 'YYYY-MM')` converts dates to strings for flexible comparison
- **Impact**: Enabled precise seasonal analysis across 24-month dataset

**Time-Based Categorization Logic**
- Initially overwhelmed by creating shift categories from raw time data
- Discovered CTEs + CASE statements provide clean, scalable solution for conditional logic
- **Impact**: Streamlined staffing analysis revealing 36% of orders occur during afternoon shift

**Window Functions (Ongoing)**
- EXTRACT() with PARTITION BY required multiple iterations to fully grasp
- Breaking complex queries into subqueries improved understanding
- **Status**: Successfully ranking months by year, continuing to deepen window function proficiency

##📚 Future Enhancements

 Build interactive dashboard using Tableau/Power BI
 Implement customer lifetime value (CLV) analysis
 Add product-level profitability metrics
 Develop predictive models for demand forecasting
 Create automated reporting pipeline
 Integrate customer retention and churn analysis

 ##🎓 Learning Outcomes
This project demonstrates:

Practical SQL expertise applicable to real-world business scenarios
Ability to translate business questions into technical queries
Data cleaning and quality assurance best practices
Advanced analytical thinking and problem-solving skills
Communication of technical findings to non-technical stakeholders



##📞 Connect With Me
Interested in discussing this project or potential opportunities? Let's connect!
(https://www.linkedin.com/in/khamirwatts/)

⭐ If you found this project helpful, please consider giving it a star!
