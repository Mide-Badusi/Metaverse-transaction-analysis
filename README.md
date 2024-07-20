# Metaverse-transaction-analysis
SQL project to analyze transaction data within a metaverse ecosystem.

# Metaverse Transaction Analysis

This repository contains a comprehensive SQL project aimed at analyzing transaction data within a metaverse ecosystem. The goal is to uncover valuable insights to drive operational efficiency, enhance user experience, and bolster security measures.

 Table of Contents

- Project Overview
- SQL Queries
  - [Transaction Volume by Hour of Day](#transaction-volume-by-hour-of-day)
  - [Top Sending and Receiving Addresses](#top-sending-and-receiving-addresses)
  - [Regional Transaction Analysis](#regional-transaction-analysis)
  - [Age Distribution Analysis](#age-distribution-analysis)
  - [Correlation Between Session Duration and Transaction Amounts](#correlation-between-session-duration-and-transaction-amounts)
  - [Purchase Patterns by Age Group](#purchase-patterns-by-age-group)
  - [Risk Score Analysis](#risk-score-analysis)
  - [Anomaly Detection](#anomaly-detection)
  - [IP Address Activity](#ip-address-activity)
  - [Advanced Anomaly Detection Using Statistical Analysis](#advanced-anomaly-detection-using-statistical-analysis)
- [Project Conclusion](#project-conclusion)
- [License](#license)


 Project Overview

In the ever-evolving landscape of digital economies, understanding transaction patterns is crucial for optimizing performance, enhancing security, and making informed strategic decisions. This project examines transaction data within a metaverse ecosystem through a series of SQL queries.

 SQL Queries

Transaction Volume by Hour of Day

Understand peak transaction times to optimize system performance or marketing strategies.


SELECT hour_of_day,
    COUNT(*) AS transaction_count
FROM metaverse
GROUP BY hour_of_day
ORDER BY transaction_count DESC;


Categorize these hours into broader periods to understand transaction patterns across different times of the day.

SELECT 
    CASE 
        WHEN hour_of_day BETWEEN 0 AND 6 THEN 'Night'
        WHEN hour_of_day BETWEEN 7 AND 12 THEN 'Morning'
        WHEN hour_of_day BETWEEN 13 AND 18 THEN 'Afternoon'
        WHEN hour_of_day BETWEEN 19 AND 23 THEN 'Evening'
    END AS time_period,
    COUNT(*) AS transaction_count
FROM metaverse
GROUP BY time_period
ORDER BY transaction_count DESC;
--Most transaction occurs in the night--

Top Sending and Receiving Addresses
Identify the most active participants in transactions.

Top Sending Addresses:

SELECT sending_address,
    COUNT(*) AS transaction_count,
    SUM(amount) AS total_amount
FROM metaverse
GROUP BY sending_address
ORDER BY total_amount DESC
LIMIT 10;

Top Receiving Addresses:

SELECT receiving_address,
    COUNT(*) AS transaction_count,
    SUM(amount) AS total_amount
FROM metaverse
GROUP BY receiving_address
ORDER BY total_amount DESC
LIMIT 10;


Regional Transaction Analysis
Compare transaction activities across different regions.

SELECT 
    location_region,
    COUNT(*) AS transaction_count,
    SUM(amount) AS total_amount
FROM metaverse
GROUP BY location_region
ORDER BY total_amount DESC;


Age Distribution Analysis
Compare transaction activities across different age groups.

SELECT 
    age_group,
    COUNT(*) AS transaction_count,
    SUM(amount) AS total_amount
FROM metaverse
GROUP BY age_group
ORDER BY total_amount DESC;


Correlation Between Session Duration and Transaction Amounts
Explore if longer sessions correlate with higher transaction amounts.

SELECT 
    session_duration,
    AVG(amount) AS avg_amount,
    SUM(amount) AS total_amount
FROM metaverse
GROUP BY session_duration
ORDER BY session_duration;
--The sessions has no effect on the transaction amounts--

Purchase Patterns by Age Group
Determine popular purchase patterns across different age groups.

SELECT age_group,
    purchase_pattern,
    COUNT(*) AS pattern_count
FROM metaverse
GROUP BY age_group, 
         purchase_pattern
ORDER BY age_group, 
         pattern_count DESC;


Risk Score Analysis
Analyze the distribution of risk scores and identify high-risk transactions.

SELECT 
    CASE 
        WHEN risk_score BETWEEN 0 AND 33 THEN 'Low'
        WHEN risk_score BETWEEN 34 AND 66 THEN 'Medium'
        WHEN risk_score BETWEEN 67 AND 100 THEN 'High'
    END AS risk_category,
    COUNT(*) AS transaction_count
FROM metaverse
GROUP BY risk_category
ORDER BY risk_category;
--Most transactions fall under medium risk--

Anomaly Detection
List all transactions marked as anomalies for further investigation.
SELECT 
    timestamp,
    sending_address,
    receiving_address,
    amount,
    transaction_type,
    location_region,
    ip_prefix,
    login_frequency,
    session_duration,
    purchase_pattern,
    age_group,
    risk_score
FROM metaverse
WHERE anomaly IS NOT NULL
ORDER BY timestamp;


IP Address Activity
Track activity from specific IP address prefixes to identify patterns or suspicious behavior.

SELECT 
    ip_prefix,
    COUNT(*) AS transaction_count
FROM metaverse
GROUP BY ip_prefix
ORDER BY transaction_count DESC;


Advanced Anomaly Detection Using Statistical Analysis
Calculate the average and standard deviation of transaction amounts to identify transactions that deviate significantly from the norm.

WITH AddressStats AS (
    SELECT sending_address,
        AVG(amount) AS avg_amount,
        STDDEV(amount) AS stddev_amount
    FROM metaverse
    GROUP BY sending_address
)
SELECT m.timestamp,
    m.sending_address,
    m.receiving_address,
    m.amount,
    m.transaction_type,
    m.location_region,
    m.ip_prefix,
    m.login_frequency,
    m.session_duration,
    m.purchase_pattern,
    m.age_group,
    m.risk_score,
    m.anomaly,
    stats.avg_amount,
    stats.stddev_amount
FROM metaverse m
JOIN AddressStats stats 
ON m.sending_address = stats.sending_address
WHERE m.amount > stats.avg_amount + 2 * stats.stddev_amount
ORDER BY m.timestamp;
--All are within a range--

Project Conclusion
Through a meticulous analysis of transaction data within the metaverse, we uncover valuable insights that drive operational efficiency, enhance user experience, and bolster security measures. These SQL queries offer a robust framework for extracting actionable intelligence from complex datasets, ensuring businesses can make data-driven decisions to stay ahead in the digital economy.
