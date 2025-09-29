# plsql-window-functions-26974_Ndirima-Mutagoma_Caleb

Business Context
IKAWA Retail Corporation, a multi-region retail company operating in the consumer goods industry, needs to enhance its sales analytics capabilities to support data-driven decision making across marketing, inventory management, and regional operations.

Data Challenge
The company lacks comprehensive analytical insights into customer purchasing patterns, product performance across regions, and sales trends over time. Current reporting provides basic aggregates but fails to deliver the nuanced insights needed for strategic planning and operational optimization.

Expected Outcome
Implement advanced window functions to identify top-performing products by region, analyze sales trends, segment customers by value, and provide actionable insights for inventory planning and targeted marketing campaigns.

Top 5 Products per Region/Quarter → Implement using RANK() and partitioning

Running Monthly Sales Totals → Calculate using SUM() OVER() with ordered frames

Month-over-Month Growth Analysis → Compute using LAG() for period comparisons

Customer Value Segmentation → Segment using NTILE(4) for quartile analysis

3-Month Moving Averages → Implement using AVG() OVER() with sliding windows
<img width="1329" height="359" alt="1" src="https://github.com/user-attachments/assets/9d8019ec-1d49-439d-b315-280c82076052" />
<img width="1364" height="428" alt="2" src="https://github.com/user-attachments/assets/58a43b76-2935-4ada-ba72-a222c0728413" />
<img width="1354" height="226" alt="3" src="https://github.com/user-attachments/assets/dd1d8f50-3202-4fd9-8e3d-4512d800e963" />
<img width="1360" height="594" alt="4" src="https://github.com/user-attachments/assets/5eb3f21f-f952-49d7-81a5-93ac7ce9b18a" />
<img width="1366" height="505" alt="5" src="https://github.com/user-attachments/assets/69470ba3-bd80-47c5-ac63-f2cab6f9c595" />
<img width="1351" height="516" alt="6" src="https://github.com/user-attachments/assets/7601dd5f-5716-4170-8835-5ddeb9404f7d" />
<img width="1364" height="486" alt="7" src="https://github.com/user-attachments/assets/596f5d9d-454b-4276-a546-0361e3bdd8de" />
<img width="1366" height="660" alt="8" src="https://github.com/user-attachments/assets/8542e8d6-5225-4431-b2a2-52ddcd37d14a" />
<img width="1366" height="504" alt="9" src="https://github.com/user-attachments/assets/93bc137f-b106-43cb-9518-56529eddf5ac" />
<img width="1364" height="558" alt="10" src="https://github.com/user-attachments/assets/c037b1ff-ee62-42fe-9bfb-2f7ded1ab738" />
<img width="1141" height="536" alt="11" src="https://github.com/user-attachments/assets/e08c61f2-f924-4834-bd4e-c2d329e91390" />
<img width="1364" height="687" alt="12" src="https://github.com/user-attachments/assets/f2ea47be-75c9-45f5-b3f9-8ec0a7267c70" />
<img width="1137" height="511" alt="13" src="https://github.com/user-attachments/assets/8c6fd79e-d82e-4929-b87c-92fd4ccc30bf" />
<img width="1366" height="648" alt="14" src="https://github.com/user-attachments/assets/7a6cf4a4-de4e-4e74-b443-fb735f778852" />

-------------------ERD-----------------------------------------
+-------------+       +----------------+       +-------------+
|  customers  |       |  transactions  |       |  products   |
+-------------+       +----------------+       +-------------+
| customer_id |<----->|  transaction_id |      |  product_id |
| name        |       |  customer_id   |<---->|  name       |
| region      |       |  product_id    |      |  category   |
+-------------+       |  sale_date     |      +-------------+
                      |  amount        |
                      +----------------+

Business Insight:
Identified John Doe as the top revenue-generating customer with 75,000 total spend. The ranking functions successfully tiered customers, revealing that the top 25% of customers generate approximately 40% of total revenue. This enables targeted retention strategies for high-value customers.


Business Insight:
Revealed a consistent upward sales trend with running totals growing from 25,000 in January to 240,500 by April. The 2-month moving average showed stabilization after initial growth, indicating market saturation that requires new customer acquisition strategies.


Business Insight:
February showed remarkable 140% month-over-month growth, potentially indicating successful marketing campaigns or seasonal demand. However, March saw a -17.39% decline, suggesting the need for sustained promotional activities to maintain growth momentum.


Business Insight:
Successfully segmented customers into four distinct value quartiles. The analysis revealed that customers in the top quartile have spending levels 25% higher than those in the third quartile, providing clear segmentation for tiered loyalty programs and personalized marketing approaches.


Descriptive Analysis: What Happened?
Revenue Distribution: Total revenue of 240,500 distributed across 4 months with clear growth trajectory

Customer Performance: John Doe emerged as premium customer (75,000), while Bob Johnson showed lowest spending (56,000)

Temporal Patterns: Sales peaked in February with 58,000, then stabilized around 55,000-65,000 range

Product Performance: Coffee Beans category demonstrated consistent demand across all periods

Diagnostic Analysis: Why Did It Happen?
Growth Drivers: February's 140% growth likely resulted from successful marketing initiatives or seasonal promotions

Customer Behavior: Regional analysis shows Kigali-based customers contribute 58% of total revenue

Performance Variations: Spending disparities between customer quartiles indicate different purchasing capacities and loyalty levels

Trend Patterns: The moving average stabilization suggests either market saturation or need for product diversification

Prescriptive Analysis: What Should We Do Next?
Immediate Actions (0-3 months):
Implement Customer Tier Program: Develop Gold, Silver, Bronze tiers based on spending quartiles

Inventory Optimization: Increase stock levels for Coffee Beans category by 20% to meet consistent demand

February Campaign Analysis: Document and replicate successful February marketing strategies

Strategic Initiatives (3-12 months):
Regional Expansion: Focus on North and South regions to balance revenue distribution

Product Diversification: Introduce complementary products to address growth plateau

Customer Retention: Develop personalized engagement programs for top quartile customers

Data-Driven Forecasting: Implement predictive analytics using the established window function framework

------------------------------WINDOWS FUNCTIONS-------------------------------------------------------



-- Customer revenue ranking analysis
SELECT 
    c.customer_id,
    c.name,
    c.region,
    SUM(t.amount) as total_revenue,
    ROW_NUMBER() OVER (ORDER BY SUM(t.amount) DESC) as row_num,
    RANK() OVER (ORDER BY SUM(t.amount) DESC) as rank,
    DENSE_RANK() OVER (ORDER BY SUM(t.amount) DESC) as dense_rank,
    PERCENT_RANK() OVER (ORDER BY SUM(t.amount) DESC) as percent_rank
FROM transactions t
JOIN customers c ON t.customer_id = c.customer_id
GROUP BY c.customer_id, c.name, c.region;


-- Running totals and moving averages
SELECT 
    DATE_FORMAT(sale_date, '%Y-%m') as month,
    SUM(amount) as monthly_sales,
    SUM(SUM(amount)) OVER (
        ORDER BY DATE_FORMAT(sale_date, '%Y-%m') 
        ROWS UNBOUNDED PRECEDING
    ) as running_total,
    AVG(SUM(amount)) OVER (
        ORDER BY DATE_FORMAT(sale_date, '%Y-%m')
        RANGE BETWEEN INTERVAL 1 MONTH PRECEDING AND CURRENT ROW
    ) as moving_avg_2month
FROM transactions
GROUP BY DATE_FORMAT(sale_date, '%Y-%m')
ORDER BY month;




-- Period-over-period growth analysis
WITH monthly_sales AS (
    SELECT 
        DATE_FORMAT(sale_date, '%Y-%m') as month,
        SUM(amount) as sales
    FROM transactions
    GROUP BY DATE_FORMAT(sale_date, '%Y-%m')
)
SELECT 
    month,
    sales as current_month_sales,
    LAG(sales) OVER (ORDER BY month) as previous_month_sales,
    LEAD(sales) OVER (ORDER BY month) as next_month_sales,
    ROUND(((sales - LAG(sales) OVER (ORDER BY month)) / 
          LAG(sales) OVER (ORDER BY month)) * 100, 2) as growth_percentage
FROM monthly_sales;


-- Customer value segmentation
SELECT 
    c.customer_id,
    c.name,
    c.region,
    SUM(t.amount) as total_spent,
    NTILE(4) OVER (ORDER BY SUM(t.amount)) as spending_quartile,
    CUME_DIST() OVER (ORDER BY SUM(t.amount)) as cumulative_distribution
FROM transactions t
JOIN customers c ON t.customer_id = c.customer_id
GROUP BY c.customer_id, c.name, c.region;





