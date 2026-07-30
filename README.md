CREATE DATABASE restaurant_analytics;

USE restaurant_analytics;

SHOW VARIABLES LIKE 'secure_file_priv';

# Table 1
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    gender VARCHAR(20),
    city VARCHAR(50),
    signup_date DATE,
    loyalty_member VARCHAR(10)
);

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/customers.csv'
INTO TABLE customers
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
(customer_id,customer_name,gender,city,signup_date,loyalty_member);

SELECT * FROM customers;
SELECT COUNT(*) FROM customers;

DROP TABLE restaurants;
# Table 2
CREATE TABLE restaurants(
    restaurant_id INT PRIMARY KEY,
    restaurant_name VARCHAR(100),
    city VARCHAR(50),
    opening_date DATE,
    seating_capacity INT
);

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/restaurants.csv'
INTO TABLE restaurants
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
(restaurant_id,
 restaurant_name,
 city,
 opening_date,
 seating_capacity);
 
SELECT COUNT(*) FROM restaurants;

# Table 3
CREATE TABLE menu_items(
    item_id INT PRIMARY KEY,
    item_name VARCHAR(100),
    category VARCHAR(50),
    cuisine VARCHAR(50),
    cost_price DECIMAL(10,2),
    selling_price DECIMAL(10,2),
    profit_per_item DECIMAL(10,2),
    profit_margin DECIMAL(10,2),
    preparation_time_minutes INT
);

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/menu_items.csv'
INTO TABLE menu_items
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
(item_id,item_name,category,cuisine,cost_price,selling_price,
profit_per_item,profit_margin,preparation_time_minutes);

SELECT COUNT(*) FROM menu_items;

DROP TABLE orders;
# Table 4
CREATE TABLE orders(
    order_id INT PRIMARY KEY,
    customer_id INT,
    restaurant_id INT,
    order_date DATE,
    order_time TIME,
    order_status VARCHAR(30),
    order_day VARCHAR(20),
    order_hour INT,
    delivery_time_minutes INT,
    total_amount DECIMAL(10,2),

    FOREIGN KEY(customer_id)
        REFERENCES customers(customer_id),

    FOREIGN KEY(restaurant_id)
        REFERENCES restaurants(restaurant_id)
);

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/orders.csv'
INTO TABLE orders
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
(order_id,customer_id,restaurant_id,order_date,order_time,
order_status,order_day,order_hour,
delivery_time_minutes,total_amount);

SELECT COUNT(*) FROM orders;

# Table 5
CREATE TABLE order_items(
    order_item_id INT PRIMARY KEY,
    order_id INT,
    item_id INT,
    quantity INT,
    item_price DECIMAL(10,2),
    item_total DECIMAL(10,2),
    net_sales DECIMAL(10,2),
    discount_flag VARCHAR(10),
    discount_amount DECIMAL(10,2),

    FOREIGN KEY(order_id)
        REFERENCES orders(order_id),

    FOREIGN KEY(item_id)
        REFERENCES menu_items(item_id)
);

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/order_items.csv'
INTO TABLE order_items
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
(order_item_id,order_id,item_id,quantity,item_price,
item_total,net_sales,discount_flag,discount_amount);

SELECT COUNT(*) FROM order_items;

DROP TABLE payments;
# Table 6
CREATE TABLE payments(
    payment_id INT PRIMARY KEY,
    order_id INT,
    payment_method VARCHAR(30),
    payment_status VARCHAR(20),
    payment_date DATE,
    amount_paid DECIMAL(10,2),

    FOREIGN KEY(order_id)
        REFERENCES orders(order_id)
);

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/payments.csv'
INTO TABLE payments
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
(payment_id,order_id,payment_method,
payment_status,payment_date,amount_paid);

SELECT COUNT(*) FROM payments;

DROP TABLE delivery_partners;
# Table 7
CREATE TABLE delivery_partners(
    delivery_partner_id INT PRIMARY KEY,
    partner_name VARCHAR(100),
    vehicle_type VARCHAR(30),
    joining_date DATE
);

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/delivery_partners.csv'
INTO TABLE delivery_partners
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
(delivery_partner_id,partner_name,
vehicle_type,joining_date);

SELECT COUNT(*) FROM delivery_partners;

DROP TABLE delivery;
# Table 8
CREATE TABLE delivery(
    delivery_id INT PRIMARY KEY,
    order_id INT,
    delivery_partner_id INT,
    delivery_distance_km DECIMAL(5,2),
    delivery_time_minutes INT,
    delay_flag VARCHAR(10),
    delay_time_bucket VARCHAR(20),
    delivery_status VARCHAR(30),

    FOREIGN KEY(order_id)
        REFERENCES orders(order_id),

    FOREIGN KEY(delivery_partner_id)
        REFERENCES delivery_partners(delivery_partner_id)
);

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/delivery.csv'
INTO TABLE delivery
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
(delivery_id,order_id,delivery_partner_id,
delivery_distance_km,delivery_time_minutes,
delay_flag,delay_time_bucket,delivery_status);

SELECT COUNT(*) FROM delivery;

# Table 9
CREATE TABLE ratings_reviews(
    review_id INT PRIMARY KEY,
    order_id INT,
    customer_id INT,
    rating INT,
    review_text VARCHAR(255),
    low_rating_flag VARCHAR(10),
    review_date DATE,

    FOREIGN KEY(order_id)
        REFERENCES orders(order_id),

    FOREIGN KEY(customer_id)
        REFERENCES customers(customer_id)
);

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/ratings_reviews.csv'
INTO TABLE ratings_reviews
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
(review_id,order_id,customer_id,rating,
review_text,low_rating_flag,review_date);

SELECT COUNT(*) FROM ratings_reviews;

# Verifying all the tables

SELECT * FROM customers;
SELECT * FROM restaurants;
SELECT * FROM menu_items;
SELECT * FROM orders;
SELECT * FROM order_items;
SELECT * FROM payments;
SELECT * FROM delivery_partners;
SELECT * FROM delivery;
SELECT * FROM ratings_reviews;

# Check relationships

SELECT
o.order_id,
c.customer_name,
r.restaurant_name
FROM orders o
JOIN customers c
ON o.customer_id = c.customer_id
JOIN restaurants r
ON o.restaurant_id = r.restaurant_id
LIMIT 10;

-- ==============================================================================
-- DASHBOARD 1 : Hidden profit killers(Menu profiatbility vs Popularity)
-- ============================================================================

# A.] Total quantity sold: Shows the most popular menu items.

SELECT
m.item_name,
SUM(oi.quantity) AS total_quantity_sold
FROM order_items oi
JOIN menu_items m
ON oi.item_id = m.item_id
GROUP BY m.item_name
ORDER BY total_quantity_sold DESC;

# B.] Total Revenue: Shows which items earn the highest revenue.

SELECT
m.item_name,
SUM(oi.net_sales) AS total_revenue
FROM order_items oi
JOIN menu_items m
ON oi.item_id = m.item_id
GROUP BY m.item_name
ORDER BY total_revenue DESC;

# C.] Total Profit: Calculates profit generated by each menu item.

SELECT
m.item_name,
SUM(m.profit_per_item * oi.quantity) AS total_profit
FROM order_items oi
JOIN menu_items m
ON oi.item_id = m.item_id
GROUP BY m.item_name
ORDER BY total_profit DESC;

# D.] Final Hidden Profit Killers Query

SELECT
m.item_name,
SUM(oi.quantity) AS quantity_sold,
SUM(oi.net_sales) AS total_sales,
SUM(m.profit_per_item * oi.quantity) AS total_profit,
ROUND(AVG(m.profit_margin),2) AS avg_profit_margin,
    CASE
        WHEN AVG(m.profit_margin) < 35 THEN 'Review Pricing'
        WHEN AVG(m.profit_margin) BETWEEN 35 AND 45 THEN 'Healthy'
        ELSE 'High Profit'
    END AS profitability_status

FROM order_items oi
JOIN menu_items m
ON oi.item_id = m.item_id
GROUP BY m.item_name
ORDER BY quantity_sold DESC;

-- ==============================================================================
-- DASHBOARD 2 : Smart Discount Optimization 
-- =============================================================================

# A.] Discount by day

SELECT
    o.order_day,
    SUM(oi.discount_amount) AS total_discount
FROM orders o
JOIN order_items oi
ON o.order_id = oi.order_id
GROUP BY o.order_day
ORDER BY total_discount DESC;

# B.] Revenue after Discount

SELECT
    o.order_day,
    SUM(oi.net_sales) AS total_revenue
FROM orders o
JOIN order_items oi
ON o.order_id = oi.order_id
GROUP BY o.order_day
ORDER BY total_revenue DESC;

# C.] Profit After Discounts

SELECT
    o.order_day,
    SUM(m.profit_per_item * oi.quantity) AS total_profit
FROM orders o
JOIN order_items oi
ON o.order_id = oi.order_id
JOIN menu_items m
ON oi.item_id = m.item_id
GROUP BY o.order_day
ORDER BY total_profit DESC;

# D.] Discount usage

SELECT
discount_flag,
COUNT(*) AS total_orders,
SUM(discount_amount) AS total_discount
FROM order_items
GROUP BY discount_flag;

# E.] Final Dashboard query

SELECT
    o.order_day,
    COUNT(DISTINCT o.order_id) AS total_orders,
    SUM(oi.discount_amount) AS total_discount,
    SUM(oi.net_sales) AS total_revenue,
    SUM(m.profit_per_item * oi.quantity) AS total_profit,
    ROUND(AVG(oi.discount_amount),2) AS avg_discount,
    CASE
        WHEN SUM(m.profit_per_item * oi.quantity) >= 140000
            THEN 'Best Day for Discounts'

        WHEN SUM(m.profit_per_item * oi.quantity) >= 130000
            THEN 'Use Carefully'

        ELSE 'Reduce Discounts'
    END AS recommendation

FROM orders o
JOIN order_items oi
ON o.order_id = oi.order_id
JOIN menu_items m
ON oi.item_id = m.item_id
GROUP BY o.order_day
ORDER BY total_profit DESC;


-- ==============================================================================
-- DASHBOARD 3 : Peak time staffing predictor
-- =============================================================================

SELECT
    r.restaurant_name,
    o.order_day,
    o.order_hour,
    COUNT(o.order_id) AS total_orders,
    ROUND(AVG(d.delivery_time_minutes),2) AS avg_delivery_time,
    r.seating_capacity,
    ROUND(COUNT(o.order_id) / r.seating_capacity,2) AS capacity_stress_ratio,

CASE
    WHEN COUNT(o.order_id) / r.seating_capacity >= 0.10
        THEN 'Add 2 Staff'

    WHEN COUNT(o.order_id) / r.seating_capacity >= 0.07
        THEN 'Add 1 Staff'

    ELSE 'Current Staff Sufficient'
END AS staffing_recommendation

FROM orders o
JOIN restaurants r
ON o.restaurant_id = r.restaurant_id
JOIN delivery d
ON o.order_id = d.order_id
GROUP BY
    r.restaurant_name,
    o.order_day,
    o.order_hour,
    r.seating_capacity
ORDER BY
    capacity_stress_ratio DESC,
    total_orders DESC;
    
-- ==============================================================================
-- DASHBOARD 4 : Restaurant capacity stress indicator
-- =============================================================================

SELECT
    r.restaurant_name,
    r.city,
    COUNT(o.order_id) AS total_orders,
    r.seating_capacity,
    ROUND(COUNT(o.order_id) / r.seating_capacity,2) AS capacity_stress_ratio,
    CASE
        WHEN COUNT(o.order_id) / r.seating_capacity >= 1.00
            THEN 'Critical'

        WHEN COUNT(o.order_id) / r.seating_capacity >= 0.70
            THEN 'High'

        WHEN COUNT(o.order_id) / r.seating_capacity >= 0.40
            THEN 'Moderate'

        ELSE 'Low'

    END AS stress_level

FROM restaurants r
JOIN orders o
ON r.restaurant_id = o.restaurant_id
GROUP BY
    r.restaurant_id,
    r.restaurant_name,
    r.city,
    r.seating_capacity
ORDER BY
    capacity_stress_ratio DESC;
    

-- ==============================================================================
-- DASHBOARD 5 : Delivery delay impact on revenue
-- =============================================================================

SELECT
SUM(low_rating_flag)
FROM ratings_reviews;

SELECT rating, low_rating_flag
FROM ratings_reviews
LIMIT 20;

SELECT
    d.delay_time_bucket,
    COUNT(d.delivery_id) AS total_deliveries,
    ROUND(AVG(d.delivery_time_minutes),2) AS avg_delivery_time,
    ROUND(AVG(r.rating),2) AS avg_rating,
    SUM(CASE
        WHEN r.rating <= 2 THEN 1
        ELSE 0
    END) AS low_ratings,
    CASE
        WHEN AVG(r.rating) >= 4
            THEN 'Excellent'
        WHEN AVG(r.rating) >= 3
            THEN 'Acceptable'
        ELSE 'Needs Improvement'
    END AS performance_status

FROM delivery d
JOIN orders o
ON d.order_id = o.order_id
JOIN ratings_reviews r
ON o.order_id = r.order_id
GROUP BY d.delay_time_bucket
ORDER BY avg_delivery_time;
