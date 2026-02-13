# E-COMMERCE SALES ANALYSIS
## Power BI Business Intelligence Project Report

---

**Project Author:** Nourhan Ali  
**Date:** February 2026  
**Tools Used:** Power BI Desktop, Power Query, DAX  
**Project Duration:** [10 days]

---

## EXECUTIVE SUMMARY

This report presents a comprehensive analysis of e-commerce sales data covering 100 transactions from January to April 2024. Using Microsoft Power BI, I developed an interactive dashboard that transforms raw transactional data into actionable business insights.

### Key Achievements
- Successfully integrated 4 disparate data sources into a unified Star Schema model
- Developed 9+ advanced DAX measures, including Time Intelligence calculations
- Created an executive-level interactive dashboard with 9+ visualizations
- Generated actionable insights leading to strategic recommendations

### Key Performance Indicators
| Metric | Value |
|--------|-------|
| Total Revenue | 348,220 EGP |
| Total Transactions | 100 |
| Total Customers | 15 |
| Average Order Value | 3,482 EGP |
| Time Period | Jan-Apr 2024 |

---

## 1. PROJECT OBJECTIVES

### 1.1 Business Goals
- Analyze sales performance across multiple dimensions (product, customer, time, supplier)
- Identify top-performing products and customers
- Track temporal trends and growth patterns
- Evaluate supplier contribution to revenue
- Understand customer payment preferences

### 1.2 Technical Goals
- Demonstrate proficiency in Power BI and DAX
- Implement industry-standard Star Schema data modeling
- Apply Time Intelligence for trend analysis
- Design user-friendly, executive-level dashboard
- Document project comprehensively for portfolio

---

## 2. DATA ARCHITECTURE

### 2.1 Data Sources Overview

The project utilizes four CSV files representing different aspects of the e-commerce business:

**Sales (Fact Table) - 100 Records**
- Transaction-level granularity
- Contains foreign keys to all dimension tables
- Includes measures (quantity, unit_price)
- Time-stamped with transaction date

**Customers (Dimension Table) - 15 Records**
- Customer demographics and segments
- Geographic information (city, country)
- Registration dates for cohort analysis

**Products (Dimension Table) - 10 Records**
- Product catalog with categories
- Brand and pricing information
- Cost data for future profit analysis

**Suppliers (Dimension Table) - 5 Records**
- Supplier details and contact information
- Geographic distribution
- Quality ratings

### 2.2 Star Schema Implementation

The data model follows the Star Schema pattern, industry best practice for dimensional modeling:

```
Fact Table (Center): Sales
  - Contains measurements (quantity, unit_price, total_sales)
  - Foreign keys to all dimensions
  
Dimension Tables (Points):
  - Customers (who bought?)
  - Products (what was bought?)
  - Suppliers (who supplied?)
  - DateTable (when was it bought?)
```

**Benefits of Star Schema:**
- Simplified queries and faster performance
- Intuitive structure for business users
- Optimized for aggregations
- Easy to extend with new dimensions

### 2.3 Relationships & Cardinality

All relationships follow Many-to-One cardinality from Sales (fact) to dimensions:
- Sales[customer_id] → Customers[customer_id] (Many:1)
- Sales[product_id] → Products[product_id] (Many:1)
- Sales[supplier_id] → Suppliers[supplier_id] (Many:1)
- Sales[date] → DateTable[Date] (Many:1)

---

## 3. DATA TRANSFORMATION PROCESS

### 3.1 Power Query Steps

**Data Import:**
1. Imported 4 CSV files via Power BI's Get Data
2. Opened Power Query Editor for transformation
3. Validated initial data quality and structure

**Data Cleaning:**
- Verified and corrected data types for all columns
- Checked for null values and duplicates
- Standardized text casing and formats
- Validated date formats and ranges

**Data Transformation:**
- Created calculated column: `total_sales = quantity × unit_price`
- Ensured proper data types (Text, Whole Number, Decimal, Date)
- No missing values detected in critical fields
- Applied Close & Apply to load into data model

### 3.2 Date Table Creation

Created a custom Date Table using DAX for Time Intelligence:

```dax
DateTable = 
ADDCOLUMNS(
    CALENDAR(DATE(2024,1,1), DATE(2024,12,31)),
    "Year", YEAR([Date]),
    "Month", FORMAT([Date], "MMM"),
    "MonthNumber", MONTH([Date]),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Day", DAY([Date])
)
```

**Why Date Table?**
- Essential for Time Intelligence functions
- Enables period-over-period comparisons
- Supports fiscal calendar if needed
- Allows drill-down/drill-up on time hierarchy

---

## 4. DAX MEASURES & CALCULATIONS

### 4.1 Basic Aggregation Measures

**Total Revenue**
```dax
Total Revenue = SUMX(Sales, Sales[quantity] * Sales[unit_price])
```
Uses SUMX iterator to calculate row-by-row, ensuring accuracy.

**Total Transactions**
```dax
Total Transactions = COUNTROWS(Sales)
```
Counts all transaction records.

**Total Customers**
```dax
Total Customers = DISTINCTCOUNT(Sales[customer_id])
```
Counts unique customers to avoid double-counting.

**Average Order Value**
```dax
Average Order Value = DIVIDE([Total Revenue], [Total Transactions], 0)
```
Uses DIVIDE for safe division, returning 0 if denominator is zero.

### 4.2 Time Intelligence Measures

**Previous Month Sales**
```dax
Previous Month Sales = 
CALCULATE(
    [Total Revenue],
    DATEADD(DateTable[Date], -1, MONTH)
)
```
Shifts context back one month for comparison.

**Year-to-Date Sales**
```dax
YTD Sales = 
TOTALYTD([Total Revenue], DateTable[Date])
```
Accumulates revenue from start of year to current date.

**Month-over-Month Growth %**
```dax
MoM Growth % = 
VAR CurrentMonth = [Total Revenue]
VAR PreviousMonth = [Previous Month Sales]
VAR Growth = DIVIDE(CurrentMonth - PreviousMonth, PreviousMonth) * 100
RETURN IF(ISBLANK(PreviousMonth), BLANK(), Growth)
```
Calculates percentage change, handling edge cases with IF and ISBLANK.

### 4.3 Advanced Analytics Measures

**Top Customer**
```dax
Top Customer = 
VAR TopCustomerID = 
    MAXX(
        TOPN(1, 
            SUMMARIZE(Sales, Customers[customer_name], "Revenue", [Total Revenue]),
            [Revenue], DESC
        ),
        Customers[customer_name]
    )
RETURN TopCustomerID
```
Identifies customer with highest total revenue.

**Top Product**
```dax
Top Product = 
VAR TopProductName = 
    MAXX(
        TOPN(1,
            SUMMARIZE(Sales, Products[product_name], "Revenue", [Total Revenue]),
            [Revenue], DESC
        ),
        Products[product_name]
    )
RETURN TopProductName
```
Identifies best-selling product by revenue.

---

## 5. DASHBOARD DESIGN & VISUALIZATION

### 5.1 Design Principles

**Executive-Level UX:**
- Clean, uncluttered layout
- Consistent color scheme
- Clear visual hierarchy
- Intuitive navigation

**Interactivity:**
- Cross-filtering between visuals
- Interactive slicers for filtering
- Drill-down capabilities
- Responsive design

### 5.2 Dashboard Components

**KPI Cards (Top Row)**
Eight key performance indicators provide at-a-glance business health:
1. Total Revenue (348K)
2. Total Customers (15)
3. Total Transactions (100)
4. Average Order Value (3.48K)
5. YTD Sales
6. MoM Growth %
7. Top Customer (Hassan Fathy)
8. Top Product (External Hard Drive 1TB)

**Charts & Visuals**

*Revenue by Category (Pie Chart)*
- Shows product category distribution
- Reveals Electronics as dominant category
- Interactive slice selection

*Top 5 Products (Bar Chart)*
- Horizontal bars for easy comparison
- Top N filter applied
- Sorted descending by revenue

*Revenue Over Time (Line Chart)*
- Time-series trend analysis
- Monthly granularity
- Identifies seasonal patterns

*Customer Segment Analysis (Donut Chart)*
- Premium vs VIP vs Regular breakdown
- Percentage and absolute values
- Color-coded segments

*Payment Method Comparison (Column Chart)*
- Credit Card vs Cash vs Debit Card
- Reveals digital payment preference
- Opportunities for payment incentives

*Geographic Distribution (Map)*
- City-level revenue visualization
- Bubble size represents revenue
- Interactive zoom and pan

*Supplier Performance Matrix*
- Rows: Supplier names
- Columns: Months
- Values: Revenue contribution
- Heat map formatting for quick insights

*Detailed Supplier Table*
- Supplier name, revenue, transaction count
- Sortable columns
- Drill-through capability

**Interactive Slicers**
Four slicers enable dynamic filtering:
- Date Range (Between selector)
- Payment Method (Multi-select)
- Customer Segment (Multi-select)
- Product Category (Multi-select)

### 5.3 Color Scheme & Formatting

- **Primary Color:** Professional blue tones
- **Accent Color:** Orange for highlights
- **Background:** Light neutral for readability
- **Font:** Clean sans-serif (Segoe UI)
- **Consistency:** Uniform styling across all visuals

---

## 6. KEY INSIGHTS & FINDINGS

### 6.1 Revenue Analysis

**Overall Performance:**
- Total revenue of 348,220 EGP across 100 transactions
- Average order value of 3,482 EGP indicates mid-to-high ticket items
- Revenue concentrated in Q1 2024 (January-April)

**Trend Observation:**
- Month-over-month growth tracking shows performance variability
- YTD tracking enables cumulative performance monitoring
- Data spans 4 months, limiting long-term trend analysis

### 6.2 Product Performance

**Category Distribution:**
- Electronics dominates the product mix
- Accessories represent smaller but consistent revenue stream
- Category concentration indicates niche market focus

**Top Products:**
1. External Hard Drive 1TB - Highest revenue contributor
2. Laptop Bag - Strong performer in accessories
3. Gaming Keyboard - High-value electronics item
4. (Additional products visible in Top 5 chart)

**Implications:**
- Electronics expertise is a competitive advantage
- Opportunity to expand accessory offerings
- High-value items drive significant revenue

### 6.3 Customer Insights

**Customer Base:**
- 15 active customers in 4-month period
- Mix of Premium (moderate spenders), VIP (high spenders), and Regular (baseline) segments
- Hassan Fathy identified as top customer by total revenue

**Segment Distribution:**
- Premium and VIP segments likely drive majority of revenue
- Regular segment represents growth opportunity
- Customer concentration risk if top customers reduce spending

**Geographic Spread:**
- Customers concentrated in major Egyptian cities
- Cairo, Alexandria, Giza are primary markets
- Opportunity for geographic expansion

### 6.4 Supplier Analysis

**Supplier Concentration:**
- 5 suppliers serve the business
- ElectroMax Supply is top supplier (94,300 EGP)
- Revenue distribution shows some concentration risk

**Supplier Performance:**
- Monthly matrix reveals supplier consistency
- Digital Warehouse and Global Tech Partners also significant
- Diverse supplier base reduces dependency risk

**Strategic Considerations:**
- Negotiate volume discounts with top suppliers
- Develop backup suppliers for key products
- Monitor supplier quality and reliability

### 6.5 Payment Behavior

**Payment Methods:**
- Credit Card is the most popular payment option
- Cash transactions represent smaller portion
- Debit Card shows moderate usage

**Digital Adoption:**
- High card payment rate indicates tech-savvy customer base
- Opportunity for digital loyalty programs
- Potential for cashback/rewards on card payments

**Business Implications:**
- Maintain payment processing relationships
- Consider digital wallet integrations
- Minimize cash handling costs

### 6.6 Temporal Patterns

**Monthly Trends:**
- January-April data shows seasonal variation
- Growth rates fluctuate month-to-month
- Need full-year data to identify true seasonality

**Day/Week Patterns:**
- Data available for day-level analysis (visible in detailed table)
- Opportunity to analyze weekday vs weekend sales
- Potential for targeted marketing based on temporal patterns

---

## 7. BUSINESS RECOMMENDATIONS

### 7.1 Revenue Growth Strategies

1. **Expand Product Portfolio**
   - Leverage electronics expertise to add complementary products
   - Increase accessory offerings with higher margins
   - Consider bundling strategies (laptop + bag + accessories)

2. **Customer Acquisition**
   - Current base of 15 customers is small - prioritize growth
   - Target similar demographics to current VIP/Premium segments
   - Develop referral program leveraging satisfied customers

3. **Average Order Value Optimization**
   - Current AOV of 3,482 EGP is healthy
   - Implement upselling strategies at checkout
   - Create product bundles to increase basket size

### 7.2 Customer Retention & Development

1. **Segment-Specific Strategies**
   - VIP: Exclusive access, early product releases, premium support
   - Premium: Loyalty rewards, upgrade path to VIP
   - Regular: Engagement campaigns to increase purchase frequency

2. **Top Customer Management**
   - Hassan Fathy (top customer) requires special attention
   - Personalized communication and offers
   - Request feedback on product selection and service

3. **Geographic Expansion**
   - Penetrate underserved cities beyond Cairo/Alexandria
   - Targeted marketing in secondary cities
   - Consider logistics partnerships for broader reach

### 7.3 Operational Efficiency

1. **Supplier Relationship Management**
   - Negotiate better terms with ElectroMax Supply (top supplier)
   - Diversify to reduce concentration with any single supplier
   - Implement supplier scorecard for performance tracking

2. **Inventory Optimization**
   - Stock popular items (External Hard Drive, Gaming Keyboard)
   - Reduce slow-moving inventory
   - Implement just-in-time for lower-value items

3. **Payment Processing**
   - Negotiate lower processing fees given high card volume
   - Explore digital wallets (Apple Pay, Google Pay)
   - Incentivize preferred payment methods with discounts

### 7.4 Data & Analytics

1. **Expand Data Collection**
   - Add profit margin data for profitability analysis
   - Collect customer acquisition source
   - Track inventory turnover rates

2. **Advanced Analytics**
   - Implement customer lifetime value (CLV) calculations
   - Develop churn prediction models
   - Create demand forecasting for inventory planning

3. **Reporting Cadence**
   - Weekly KPI monitoring for agility
   - Monthly deep-dive reviews with stakeholders
   - Quarterly strategic planning sessions

---

## 8. TECHNICAL LEARNINGS

### 8.1 Power Query Mastery

**Skills Developed:**
- ETL process design and execution
- Data type management and validation
- Calculated column creation
- Multi-source data integration

**Best Practices Learned:**
- Always validate data types after import
- Use meaningful column names
- Document transformation steps
- Test transformations with sample data

### 8.2 DAX Expertise

**Key Concepts Mastered:**
- Context (row vs filter context)
- Iterator functions (SUMX, MAXX)
- Time Intelligence (DATEADD, TOTALYTD)
- Conditional logic (IF, ISBLANK)
- Safe calculations (DIVIDE with default)

**Common Pitfalls Avoided:**
- Using SUM on calculated columns with wrong data types
- Forgetting to handle BLANK values
- Not using VAR for complex calculations
- Inefficient measures with nested functions

### 8.3 Data Modeling Principles

**Star Schema Benefits:**
- Query performance optimization
- Simplified relationship management
- Business user comprehension
- Scalability for future dimensions

**Relationship Best Practices:**
- Always validate cardinality
- Use surrogate keys when possible
- Avoid circular relationships
- Mark date tables appropriately

### 8.4 Dashboard Design Insights

**UX Principles Applied:**
- F-pattern layout for visual flow
- Most important KPIs at top
- Consistent spacing and alignment
- Limited color palette for clarity

**Interactivity Lessons:**
- Slicers placement affects usability
- Cross-filtering can confuse if not intentional
- Drill-through adds depth without clutter
- Mobile layout requires different approach

---

## 9. PROJECT CHALLENGES & SOLUTIONS

### 9.1 Challenge: MoM Growth Showing Zero

**Problem:**
Initial Month-over-Month Growth % measure returned 0 for all months.

**Root Cause:**
First month (January) had no previous month data, causing BLANK division.

**Solution:**
Modified DAX to handle BLANK values:
```dax
RETURN IF(ISBLANK(PreviousMonth), BLANK(), Growth)
```

**Learning:**
Always account for edge cases in Time Intelligence calculations.

### 9.2 Challenge: Total Revenue Measure Error

**Problem:**
Measure using `SUM(Sales[total_sales])` threw error.

**Root Cause:**
Calculated column `total_sales` created in Power Query had wrong data type (Text instead of Number).

**Solution:**
Recreated measure using SUMX with row-level calculation:
```dax
Total Revenue = SUMX(Sales, Sales[quantity] * Sales[unit_price])
```

**Learning:**
Iterator functions like SUMX are more reliable than column aggregations.
Always verify data types in Power Query transformations.

### 9.3 Challenge: Map Visual Not Displaying

**Problem:**
Map visual showed blank when using only city names.

**Root Cause:**
Power BI couldn't geocode city names without country context.

**Solution:**
Added both `Customers[country]` and `Customers[city]` to Location field.

**Learning:**
Provide hierarchical geographic data for better map visualization.

### 9.4 Challenge: Limited Historical Data

**Problem:**
Only 4 months of data limits trend analysis and forecasting.

**Solution:**
- Acknowledged limitation in documentation
- Focused on available insights
- Planned for future data expansion

**Learning:**
Work with available data while documenting limitations transparently.

---

## 10. FUTURE ENHANCEMENTS

### 10.1 Short-Term Additions (1-2 months)

1. **Profit Analysis**
   - Add cost data to calculate profit margins
   - Create profit-focused measures and visuals
   - Identify most profitable products and customers

2. **Customer Lifetime Value (CLV)**
   - Calculate CLV for each customer
   - Segment customers by CLV
   - Prioritize high-value customer retention

3. **Inventory Tracking**
   - Integrate stock level data
   - Add inventory turnover metrics
   - Alert on low-stock items

### 10.2 Medium-Term Enhancements (3-6 months)

1. **Predictive Analytics**
   - Forecast future sales using Power BI's AI capabilities
   - Predict customer churn
   - Recommend optimal stock levels

2. **RFM Analysis**
   - Recency, Frequency, Monetary segmentation
   - Automated customer scoring
   - Targeted marketing campaigns

3. **Mobile Optimization**
   - Redesign for mobile viewing
   - Create phone-specific layout
   - Enable mobile notifications

### 10.3 Long-Term Vision (6+ months)

1. **Real-Time Dashboard**
   - Connect to live data sources
   - Auto-refresh at intervals
   - Real-time alerts for KPI thresholds

2. **Multi-Channel Integration**
   - Combine online and offline sales
   - Social media metrics
   - Marketing campaign ROI

3. **Advanced Machine Learning**
   - Product recommendation engine
   - Dynamic pricing optimization
   - Customer behavior prediction

---

## 11. CONCLUSION

### 11.1 Project Summary

This E-commerce Sales Analysis project successfully demonstrates comprehensive Power BI capabilities from data integration through advanced analytics to executive-level visualization. By analyzing 100 transactions across 4 months, I transformed raw CSV data into actionable business intelligence.

**Key Deliverables:**
✅ Star Schema data model with 4 related tables  
✅ 9+ advanced DAX measures including Time Intelligence  
✅ Interactive dashboard with 9+ visualizations  
✅ Strategic business recommendations  
✅ Comprehensive technical documentation  

### 11.2 Skills Validated

This project proves proficiency in:
- **Power BI Suite:** Desktop, Power Query, DAX, Visualizations
- **Data Modeling:** Star Schema, relationship management
- **Business Intelligence:** KPI development, metric tracking, insight generation
- **Data Analytics:** Trend analysis, segmentation, performance tracking
- **Communication:** Dashboard design, data storytelling, documentation

### 11.3 Business Value

The dashboard provides immediate value by:
- Centralizing sales data from multiple sources
- Enabling data-driven decision making
- Identifying revenue opportunities
- Tracking performance against goals
- Facilitating strategic planning

### 11.4 Personal Growth

Through this project, I:
- Deepened DAX expertise, especially Time Intelligence
- Mastered Star Schema implementation
- Developed UX design skills for dashboards
- Learned to balance technical depth with business clarity
- Gained confidence in end-to-end BI project delivery

### 11.5 Next Steps

Moving forward, I will:
1. Implement short-term enhancements (profit analysis, CLV)
2. Expand data collection for richer insights
3. Develop additional industry-specific dashboards
4. Share learnings through blog posts and tutorials
5. Continue advancing Power BI and analytics skills

---

## 12. APPENDICES

### Appendix A: DAX Measures Reference

| Measure Name | Formula | Category |
|--------------|---------|----------|
| Total Revenue | SUMX(Sales, [quantity] * [unit_price]) | Basic |
| Total Quantity | SUM(Sales[quantity]) | Basic |
| Total Transactions | COUNTROWS(Sales) | Basic |
| Average Order Value | DIVIDE([Total Revenue], [Total Transactions], 0) | Basic |
| Total Customers | DISTINCTCOUNT(Sales[customer_id]) | Basic |
| Previous Month Sales | CALCULATE([Total Revenue], DATEADD(DateTable[Date], -1, MONTH)) | Time Intelligence |
| YTD Sales | TOTALYTD([Total Revenue], DateTable[Date]) | Time Intelligence |
| MoM Growth % | [Complex formula - see Section 4.2] | Growth |
| Top Customer | [Complex formula - see Section 4.3] | Advanced |
| Top Product | [Complex formula - see Section 4.3] | Advanced |

### Appendix B: Data Dictionary

**Sales Table (Fact)**
| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| transaction_id | Text | Unique transaction ID | TXN001 |
| date | Date | Transaction date | 2024-01-15 |
| customer_id | Text | FK to Customers | CUST001 |
| product_id | Text | FK to Products | PROD001 |
| supplier_id | Text | FK to Suppliers | SUP001 |
| quantity | Whole Number | Units purchased | 2 |
| unit_price | Decimal | Price per unit | 1500.00 |
| payment_method | Text | Payment type | Credit Card |
| total_sales | Decimal | Calculated column | 3000.00 |

**Customers Table (Dimension)**
| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| customer_id | Text | Unique customer ID | CUST001 |
| customer_name | Text | Customer name | Ahmed Hassan |
| city | Text | Customer city | Cairo |
| country | Text | Customer country | Egypt |
| segment | Text | Customer segment | Premium |
| registration_date | Date | Registration date | 2023-05-12 |

**Products Table (Dimension)**
| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| product_id | Text | Unique product ID | PROD001 |
| product_name | Text | Product name | Wireless Mouse |
| category | Text | Product category | Electronics |
| subcategory | Text | Product subcategory | Computer Accessories |
| brand | Text | Brand name | TechPro |
| cost_price | Decimal | Cost to business | 800.00 |

**Suppliers Table (Dimension)**
| Column | Data Type | Description | Example |
|--------|-----------|-------------|---------|
| supplier_id | Text | Unique supplier ID | SUP001 |
| supplier_name | Text | Supplier name | TechSource Ltd |
| country | Text | Supplier country | Egypt |
| city | Text | Supplier city | Cairo |
| contact_person | Text | Contact name | Ahmed Khalil |
| rating | Decimal | Quality rating | 4.5 |

### Appendix C: Visual Element Reference

| Visual | Type | Purpose | Fields Used |
|--------|------|---------|-------------|
| KPI Cards | Card | Display key metrics | Various measures |
| Revenue by Category | Pie Chart | Category distribution | Products[category], Total Revenue |
| Top 5 Products | Bar Chart | Best sellers | Products[product_name], Total Revenue |
| Revenue Over Time | Line Chart | Trend analysis | DateTable[Month], Total Revenue |
| Customer Segments | Donut Chart | Segment analysis | Customers[segment], Total Revenue |
| Payment Methods | Column Chart | Payment comparison | Sales[payment_method], Total Revenue |
| Geographic Map | Map | Location distribution | Customers[city/country], Total Revenue |
| Supplier Performance | Matrix | Monthly supplier data | Suppliers, DateTable, Total Revenue |
| Supplier Details | Table | Detailed metrics | Supplier fields + measures |
| Target Gauge | Gauge | Goal tracking | Total Revenue vs Target |

### Appendix D: Project Timeline

| Phase | Duration | Key Activities |
|-------|----------|----------------|
| Planning & Setup | Day 1 | Define objectives, identify data sources |
| Data Collection | Day 1-2 | Create/obtain CSV files |
| Data Preparation | Day 2-3 | Import, clean, transform in Power Query |
| Data Modeling | Day 3-4 | Build Star Schema, create relationships |
| DAX Development | Day 4-6 | Create measures, test calculations |
| Dashboard Design | Day 6-8 | Build visuals, apply formatting |
| Testing & Refinement | Day 8-9 | Test interactivity, fix issues |
| Documentation | Day 9-10 | Create README, report, screenshots |

### Appendix E: Resources & References

**Power BI Documentation:**
- Microsoft Power BI Documentation: https://docs.microsoft.com/power-bi/
- DAX Function Reference: https://dax.guide/
- Power Query M Reference: https://docs.microsoft.com/powerquery-m/

**Learning Resources:**
- SQLBI (Marco Russo & Alberto Ferrari): https://www.sqlbi.com/
- Guy in a Cube (YouTube): https://www.youtube.com/c/GuyinaCube
- Curbal (YouTube): https://www.youtube.com/c/CurbalEN

**Design Inspiration:**
- Power BI Community Showcase
- Tableau Public Gallery
- Dribbble Dashboard Designs

---

**END OF REPORT**

---

*This report is created for educational and portfolio purposes.*  
*For questions or feedback, please contact: bio.eng.nourhanali@gmail.com*
