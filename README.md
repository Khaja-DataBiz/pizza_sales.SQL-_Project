# pizza_sales.SQL-_Project

## Introduction
Pizza businesses thrive on customer satisfaction, and behind every successful pizzeria is data that tells a story. From which pizza size flies off the shelves to which toppings contribute most to revenue, data insights drive decisions that make or break profits.

In this Retail Sales Analysis project, I use SQL to dive deep into a pizza chain's order data, uncovering key trends and metrics. This analysis reveals the highest revenue-generating pizzas, optimal sale times, and customer preferences that inform business strategy.

# Why This Analysis Matters

Understanding these metrics allows the business to:

*Streamline operations by predicting demand.
*Maximize revenue by focusing on top-performing products.
*Enhance customer satisfaction by optimizing offerings based on real data.

1. Database Setup
   
Context
A pizza chain’s operational data is spread across multiple tables: orders, orders_details, pizzas, and pizza_types. Each table stores a different aspect of the business—orders placed, details of each pizza, and the pizza categories.

Creating the Core Tables

```sql
create database pizzahut;
```

> Purpose: To create a dedicated database for analysis. This ensures data is well-organized and can be queried efficiently.

# Orders Table
   
  ```sql
 CREATE TABLE orders (
    order_id INT NOT NULL,
    order_date DATE NOT NULL,
    order_time TIME NOT NULL,
    PRIMARY KEY (order_id)
);
```
This table logs every order placed, including the time and date—critical for analyzing order patterns over time.

#Orders Details Table
   
   ```sql
   CREATE TABLE orders_details (
    order_details_id INT NOT NULL,
    order_id INT NOT NULL,
    pizza_id TEXT NOT NULL,
    quantity INT NOT NULL,
    PRIMARY KEY (order_details_id)
);
```
Storing pizza details (like type and quantity ordered) per order allows us to track customer preferences and sales volumes.

2. Business Metrics
   
# Total Number of Orders Placed

```sql
SELECT COUNT(order_id) AS total_orders
FROM orders;
```

Purpose: Knowing how many orders are placed is the first step in understanding sales activity.

Output: Provides an overview of the total order volume—helpful for measuring business growth or comparing performance across different periods.

# Total Revenue from Pizza Sales

```sql
SELECT 
    ROUND(SUM(orders_details.quantity * pizzas.price), 2) AS total_sales
FROM
    orders_details
JOIN pizzas ON pizzas.pizza_id = orders_details.pizza_id;
```

Purpose: This query calculates total sales revenue, which is essential for assessing the business's financial health.

Real-World Impact: Pizza chains can use this information to forecast earnings and assess the effectiveness of marketing campaigns or discounts.

# Highest Priced Pizza

```sql
SELECT pizza_types.name, pizzas.price
FROM pizza_types
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
```
Purpose: Knowing the highest-priced item on the menu can influence pricing strategy.

Business Use: This insight helps the pizza chain decide if the most expensive item is marketed effectively or if adjustments need to be made to improve sales of premium items.

3. Customer Preferences and Insights
   
# Most Common Pizza Size Ordered

```sql
SELECT pizzas.size, COUNT(orders_details.order_details_id) AS order_count
FROM pizzas
JOIN orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;
```

Purpose: Analyzing the most popular pizza size helps optimize inventory management and production planning.

Insight: Knowing which size is ordered the most lets the business focus on stocking the right ingredients and adjusting menu promotions.

# Top 5 Most Ordered Pizza Types

```sql
SELECT pizza_types.name, SUM(orders_details.quantity) AS quantity
FROM pizza_types
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY quantity DESC
LIMIT 5;
```

Purpose: This query identifies the top 5 most ordered pizza types, offering insight into customer preferences.

Business Use: Knowing the best-selling pizzas allows the business to spotlight these items in marketing campaigns or bundle deals.

4. Timing and Distribution

# Order Distribution by Hour of the Day

```sql
SELECT HOUR(order_time) AS hour, COUNT(order_id) AS order_count
FROM orders
GROUP BY HOUR(order_time);
```

Purpose: Understanding peak order times helps businesses allocate staff efficiently and plan inventory for high-demand periods.

Insight: Identifying peak hours allows pizzerias to run special deals or discounts to drive sales during slower periods.

# Pizza Category Distribution

```sql
SELECT category, COUNT(name)
FROM pizza_types
GROUP BY category;
```

Purpose: This query provides insight into how different categories (vegetarian, meat-based, etc.) perform.

Real-World Impact: It helps balance the menu by focusing on categories that bring in the most revenue and identifying underperforming items.

5. Advanced Performance Metrics
   
# Top 3 Pizza Types by Revenue

```sql
SELECT pizza_types.name, SUM(orders_details.quantity * pizzas.price) AS revenue
FROM pizza_types
JOIN pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
JOIN orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```

Purpose: This identifies the top 3 revenue-generating pizzas, allowing the business to focus on promoting its highest earners.

Insight: By focusing on these pizzas, the chain can increase revenue through targeted promotions or meal deals featuring these popular items.

# Percentage Contribution of Pizza Types to Total Revenue

```sql
SELECT pizza_types.category, ROUND(SUM(orders_details.quantity * pizzas.price) / 
(SELECT ROUND(SUM(orders_details.quantity * pizzas.price), 2) 
 FROM orders_details 
 JOIN pizzas ON pizzas.pizza_id = orders_details.pizza_id) * 100, 2) AS revenue_percentage
FROM pizza_types
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue_percentage DESC;
```

Purpose: This query calculates the percentage each pizza category contributes to total sales.

Insight: This helps the chain focus its marketing efforts on the highest revenue-generating categories.

6. Cumulative and Category Insights
   
# Cumulative Revenue Over Time

```sql
SELECT order_date, SUM(revenue) OVER(ORDER BY order_date) AS cum_revenue
FROM
    (SELECT orders.order_date, SUM(orders_details.quantity * pizzas.price) AS revenue
     FROM orders_details
     JOIN pizzas ON orders_details.pizza_id = pizzas.pizza_id
     JOIN orders ON orders.order_id = orders_details.order_id
     GROUP BY orders.order_date) AS sales;
```

Purpose: Tracks total revenue growth over time, helping businesses understand how sales evolve.

Real-World Impact: The pizza chain can use this data to monitor the effectiveness of new marketing strategies or product launches.

# 
Top 3 Pizzas by Revenue in Each Category

```sql
SELECT name, revenue 
FROM 
    (SELECT category, name, revenue, RANK() OVER(PARTITION BY category ORDER BY revenue DESC) AS rn
     FROM
         (SELECT pizza_types.category, pizza_types.name, 
                 SUM(orders_details.quantity * pizzas.price) AS revenue
          FROM pizza_types 
          JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
          JOIN orders_details ON orders_details.pizza_id = pizzas.pizza_id
          GROUP BY pizza_types.category, pizza_types.name) AS a) AS b
WHERE rn <= 3;
```

Purpose: This query ranks the top 3 pizzas by revenue for each category, helping the business understand which items are the top performers in their respective categories.

Insight: By knowing which pizzas are top-sellers within each category, the chain can adjust its menu to meet customer demand and increase revenue.






