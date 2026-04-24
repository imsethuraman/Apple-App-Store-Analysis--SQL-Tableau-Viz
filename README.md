# 🍎 Apple App Store Analysis (SQL + Tableau)

This project provides an in-depth analysis of the Apple App Store using **SQL (EDA)** and **Tableau (Visualization)** to uncover insights into app performance, user ratings, pricing strategy, and market trends.

---

## 📌 Project Overview

The goal of this project is to analyze App Store data and answer key business questions such as:

- Do paid apps perform better than free apps? 💰  
- How do app genres impact ratings? 🎮  
- Does app description length influence user engagement? 📝  
- Do multilingual apps receive higher ratings? 🌍  

This project simulates a **real-world product analytics case study**.

---

## 🔗 Tableau Dashboard

👉 [View Interactive Dashboard](https://public.tableau.com/views/AppleAppStoreAnalysis_16895090917270/AppleAppsStore?:language=en-GB&:display_count=n&:origin=viz_share_link)

<img width="1780" height="999" alt="Apple Apps Store" src="https://github.com/user-attachments/assets/aac71fe4-b20c-4346-a909-6e1f66ed4f31" />

---

## 🛠️ Tools & Technologies

- **SQL** (MySQL / PostgreSQL compatible)
- **Tableau** (Data Visualization)
- Data Cleaning & EDA
- Window Functions & Aggregations

---

## 📂 Dataset

- AppleStore (App-level data)
- AppleStore Description (split across multiple tables)

---

## 🧱 Data Preparation

### Combine Description Tables

```sql
CREATE TABLE applestore_description_combined AS
SELECT * FROM appleStore_description1
UNION ALL
SELECT * FROM appleStore_description2
UNION ALL
SELECT * FROM appleStore_description3
UNION ALL
SELECT * FROM appleStore_description4;


🔍 Exploratory Data Analysis (EDA)

🧾 Data Quality Checks

-- Unique apps
SELECT COUNT(DISTINCT id) FROM AppleStore;

SELECT COUNT(DISTINCT id) FROM applestore_description_combined;

-- Missing values
SELECT COUNT(*) 
FROM AppleStore
WHERE track_name IS NULL OR user_rating IS NULL OR prime_genre IS NULL;

SELECT COUNT(*) 
FROM applestore_description_combined
WHERE app_desc IS NULL;

📊 Genre Distribution

SELECT prime_genre, COUNT(*) AS Num_Apps
FROM AppleStore
GROUP BY prime_genre
ORDER BY Num_Apps DESC;

⭐ Ratings Overview

SELECT 
  MIN(user_rating) AS Min_rating,
  MAX(user_rating) AS Max_rating,
  AVG(user_rating) AS Avg_rating
FROM AppleStore;

💰 Price Analysis
-- Price distribution
SELECT
  (price/2)*2 AS Price_Bin_Start,
  ((price/2)*2)+2 AS Price_Bin_End,
  COUNT(*) AS NumApps
FROM AppleStore
GROUP BY Price_Bin_Start
ORDER BY Price_Bin_Start;

-- Price summary
SELECT 
  MIN(price) AS Min_Price,
  MAX(price) AS Max_Price,
  AVG(price) AS Avg_Price
FROM AppleStore;

📈 Business Analysis

💵 Paid vs Free Apps

SELECT 
  CASE 
    WHEN price > 0 THEN 'Paid'
    ELSE 'Free'
  END AS App_Type,
  AVG(user_rating) AS Avg_Rating
FROM AppleStore
GROUP BY App_Type;

🌍 Language Support vs Ratings

SELECT 
  CASE 
    WHEN lang_num < 10 THEN '< 10 languages'
    WHEN lang_num BETWEEN 10 AND 30 THEN '10-30 languages'
    ELSE '> 30 languages'
  END AS language_bucket,
  AVG(user_rating) AS Avg_Rating
FROM AppleStore
GROUP BY language_bucket
ORDER BY Avg_Rating DESC;

🎮 Low Rated Genres

SELECT 
  prime_genre,
  AVG(user_rating) AS Avg_Rating
FROM AppleStore
GROUP BY prime_genre
ORDER BY Avg_Rating
LIMIT 10;

📝 Description Length vs Ratings

SELECT 
  CASE 
    WHEN LENGTH(b.app_desc) < 500 THEN 'Short'
    WHEN LENGTH(b.app_desc) BETWEEN 500 AND 1000 THEN 'Medium'
    ELSE 'Long'
  END AS Description_Length,
  AVG(a.user_rating) AS Avg_Rating
FROM AppleStore a
JOIN applestore_description_combined b
ON a.id = b.id
GROUP BY Description_Length
ORDER BY Avg_Rating DESC;

🏆 Top Rated Apps by Genre

SELECT prime_genre, track_name, user_rating
FROM (
  SELECT 
    prime_genre, 
    track_name, 
    user_rating,
    RANK() OVER (
      PARTITION BY prime_genre 
      ORDER BY user_rating DESC, rating_count_tot DESC
    ) AS rank
  FROM AppleStore
) a
WHERE rank = 1;

**📈 Business Recommendations**

Focus on multi-language support to expand reach
Optimize app descriptions (detailed & engaging)
Invest in high-performing genres
Improve UX for low-rated categories
Consider a premium pricing strategy for high-quality apps



⭐ Support

If you found this project useful, consider giving it a ⭐ on GitHub!
