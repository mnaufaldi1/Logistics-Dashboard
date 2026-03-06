# 📦 Logistics & Supply Chain Performance Dashboard

## 📌 Project Overview

Simple interactive logistics dashboard tracking revenue trends, top routes &amp; customers by gross profit, driver performance (on-time rates, miles/day, maintenance costs), and fleet insights using SQL and Power BI.

---

## 🎯 Business Problem

Logistics operations often face challenges such as:

- Low on-time delivery rates  
- High transportation mileage  
- Underperforming drivers  
- Inefficient operational monitoring  

Without structured performance tracking, decision-making becomes reactive instead of data-driven.

This project simulates a real-world logistics performance monitoring system.

---

## 📊 Key Objectives

- Monitor Revenue Trend
- Top routes & company by profit insights
- Measure On-Time Delivery Rate   
- Classify Operational Risk Levels  
- Monitor Overall Logistics KPIs  

---

## 🗂 Dataset Description

The dataset includes structured logistics operational data such as:

- Customers  
- Drivers  
- Delivery Events  
- Trucks  
- Routes
- etc

For the full dataset, check the folder raw_data
> Note: This project uses a real dataset from ([Kaggle](https://www.kaggle.com/datasets/yogape/logistics-operations-database)) designed to replicate real logistics scenarios.

---

## 🛠 Tools & Technologies

- **SQL** – Data cleaning, aggregation, KPI calculation  
- **Power BI** – Data modeling & dashboard development  
- **Microsoft Excel** – Initial exploration  

---

## 🔍 Data Processing Workflow

1. Data check and validation  
2. Aggregation of KPIs using SQL queries  
3. Creation of calculated metrics (On-Time Rate, Miles per Day, Driver Category)  
4. Data modeling in Power BI  
5. Dashboard development  

---
## SQL Queries
### On-Time Delivery Rate per Driver
```sql
SELECT 
    d.driver_id,
    d.first_name,
    d.last_name,
    ROUND(
        AVG(CASE WHEN de.on_time_flag = 'true' THEN 1 ELSE 0 END)::numeric,
        2
    ) AS on_time_delivery_rate
FROM delivery_events de
JOIN trips t USING(trip_id)
JOIN drivers d USING(driver_id)
WHERE de.event_type = 'Delivery'
GROUP BY d.driver_id, d.first_name, d.last_name
ORDER BY on_time_delivery_rate DESC;
```

### Monthly Revenue and Load Volume
```sql
SELECT
    DATE_TRUNC('month', l.load_date) AS month,
    COUNT(l.load_id) AS total_loads,
    SUM(l.revenue) AS total_revenue
FROM loads l
GROUP BY DATE_TRUNC('month', l.load_date)
ORDER BY month;
```

### Profitability by Route
```sql
SELECT
    r.route_id,
    r.origin_city,
    r.destination_city,
    COUNT(l.load_id) AS total_loads,
    SUM(l.revenue) AS total_revenue,
    SUM(fp.total_cost) AS total_fuel_cost,
    SUM(l.revenue) - SUM(fp.total_cost) AS gross_profit
FROM loads l
JOIN trips t ON l.load_id = t.load_id
JOIN routes r ON l.route_id = r.route_id
LEFT JOIN fuel_purchases fp ON t.trip_id = fp.trip_id
GROUP BY r.route_id, r.origin_city, r.destination_city
ORDER BY gross_profit DESC;
```

### Customer Profitability
```sql
SELECT
    c.customer_name,
    COUNT(l.load_id) AS total_loads,
    SUM(l.revenue) AS total_revenue,
    SUM(fp.total_cost) AS total_fuel_cost,
    SUM(l.revenue) - SUM(fp.total_cost) AS gross_profit
FROM loads l
JOIN customers c ON l.customer_id = c.customer_id
JOIN trips t ON l.load_id = t.load_id
LEFT JOIN fuel_purchases fp ON t.trip_id = fp.trip_id
GROUP BY c.customer_name
ORDER BY gross_profit DESC;
```

### Driver Productivity
```sql
SELECT
    d.driver_id,
    d.first_name,
    d.last_name,
    COUNT(DISTINCT t.dispatch_date) AS active_days,
    SUM(t.actual_distance_miles) AS total_miles,
    ROUND(
        SUM(t.actual_distance_miles) /
        NULLIF(COUNT(DISTINCT t.dispatch_date), 0),
        2
    ) AS miles_per_day
FROM trips t
JOIN drivers d ON t.driver_id = d.driver_id
GROUP BY d.driver_id, d.first_name, d.last_name
ORDER BY miles_per_day DESC;
```

### Maintenance Cost per Mile by Truck
```sql
SELECT
    tr.truck_id,
    tr.make,
    tr.model_year,
    SUM(m.total_cost) AS total_maintenance_cost,
    SUM(t.actual_distance_miles) AS total_miles,
    ROUND(
        SUM(m.total_cost) /
        NULLIF(SUM(t.actual_distance_miles), 0),
        2
    ) AS maintenance_cost_per_mile
FROM trucks tr
LEFT JOIN maintenance_records m ON tr.truck_id = m.truck_id
LEFT JOIN trips t ON tr.truck_id = t.truck_id
GROUP BY tr.truck_id, tr.make, tr.model_year
ORDER BY maintenance_cost_per_mile DESC;
```
## 📈 Dashboard Features

### 🚚 Financial Analysis
- Monthly Revenue Trend Analysis
- Top Routes by Gross Profit
- Customer Profitability Comparison

### 👤 Operational Analysis
- Performance Comparison Table (On-Time Rate & Miles per Day)
- Risk Flag Classification of Drivers
- Fleet Performance Graph


---

## 📷 Dashboard Preview
![Logistic Dashboard_pages-to-jpg-0001](https://github.com/user-attachments/assets/42f91b46-93a2-480f-829e-464ce196659f)
![Logistic Dashboard_pages-to-jpg-0002](https://github.com/user-attachments/assets/28d7a349-a0eb-441e-855a-24e7d290fe1b)

---

## 💡 Key Insights

- February consistently shows the lowest revenue across all years, suggesting possible seasonal demand fluctuations.
- Several routes generate around $22M gross profit (e.g., Philadelphia–Seattle, Charlotte–Portland), indicating high-value logistics corridors.
- The top customer (First Group) contributes $18.3M in gross profit, significantly outperforming other customers.
- Reliable and Top Performer drivers average ~1,900–2,000 miles per day with on-time rates close to 0.48–0.50.
- Higher mileage does not always translate to better on-time performance, indicating potential operational trade-offs between speed and reliability.
- Older trucks tend to have higher maintenance cost per mile compared to newer models.
- Underperforming drivers account for about 26% of the workforce, representing a potential opportunity for training or operational improvement.

---

## 🚀 Future Improvements
Dive deeper into the analysis
- Cost per mile/load
- Fuel efficiency (MPG trends by driver/route/model)
- Behavior scoring (harsh braking, speeding, idling events)
- Predictive delay risk modeling using historical delivery data
- Route optimization analysis to identify more efficient delivery paths
- etc

---

## 👤 About the Author
**Muhammad Naufaldi**  
Physics graduate with interest in technology, innovation, and data analysis.

### 🌐 Contact
- **LinkedIn:** https://www.linkedin.com/in/muhammad-naufaldi1/
- **Portfolio:** https://linktr.ee/PortofolioNaufaldi
