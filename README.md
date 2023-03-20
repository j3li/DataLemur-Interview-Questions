# DataLemur Interview Questions
[DataLemur](https://datalemur.com) is a data science platform with real interview questions from top companies, including FAANG companies.

I will be sharing my answers to SQL problems.


## Level: Easy

### ✏️ LinkedIn | Data Science Skills
[Question: ](https://datalemur.com/questions/matching-skills) Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL.

Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.

```sql
SELECT a.candidate_id FROM candidates a, candidates b, candidates c
WHERE a.candidate_id = b.candidate_id AND a.candidate_id = c.candidate_id
AND a.skill = 'Python' AND b.skill = 'Tableau' AND c.skill = 'PostgreSQL'
ORDER BY a.candidate_id;
```
![image](https://user-images.githubusercontent.com/50200083/221438378-ff0c1d82-e428-4bec-bfb7-053c519e5aa1.png)

### ✏️ Facebook | Page With No Likes
[Question: ](https://datalemur.com/questions/sql-page-with-no-likes) Assume you are given the tables below about Facebook pages and page likes. Write a query to return the page IDs of all the Facebook pages that don't have any likes. The output should be in ascending order.

```sql
SELECT page_id
FROM pages
WHERE NOT EXISTS (
  SELECT 1
  FROM page_likes AS likes
  WHERE likes.page_id = pages.page_id)
ORDER BY page_id ASC;
```
![image](https://user-images.githubusercontent.com/50200083/221440011-9070a9a0-d874-4117-bbb8-a41807a2228d.png)

### ✏️ Tesla | Unfinished Parts
[Question: ](https://datalemur.com/questions/tesla-unfinished-parts) Tesla is investigating bottlenecks in their production, and they need your help to extract the relevant data. Write a query that determines which parts have begun the assembly process but are not yet finished.

```sql
SELECT DISTINCT part 
FROM parts_assembly
WHERE finish_date IS NULL;
```
![image](https://user-images.githubusercontent.com/50200083/221440143-6f087f90-6f2e-4785-8d1d-a4a9aa4205cc.png)  



## Level: Medium



### ✏️ Uber | User's Third Transaction
[Question: ](https://datalemur.com/questions/sql-third-transaction) Assume you are given the table below on Uber transactions made by users. Write a query to obtain the third transaction of every user. Output the user id, spend and transaction date.

```sql
with rows as (
SELECT *, ROW_NUMBER() OVER(PARTITION BY user_id ORDER BY transaction_date) as number FROM transactions
)
SELECT user_id, spend, transaction_date
FROM rows
WHERE number = 3;
```
![image](https://user-images.githubusercontent.com/50200083/222848163-a100356a-a3f8-41c8-9de6-d8c19a0b2f88.png)

### ✏️ Snapchat | Sending vs. Opening Snaps
[Question: ](https://datalemur.com/questions/time-spent-snaps) Assume you are given the tables below containing information on Snapchat users, their ages, and their time spent sending and opening snaps. Write a query to obtain a breakdown of the time spent sending vs. opening snaps (as a percentage of total time spent on these activities) for each age group.

Output the age bucket and percentage of sending and opening snaps. Round the percentage to 2 decimal places.

```sql
with joined as (
SELECT a.activity_type, a.time_spent, ab.age_bucket FROM activities a 
JOIN age_breakdown ab
ON a.user_id = ab.user_id)

SELECT age_bucket, 
       ROUND((SUM(time_spent) FILTER(WHERE activity_type = 'send'))/(SUM(time_spent) FILTER(WHERE activity_type IN ('send', 'open')))*100.0, 2) as send_perc,
       ROUND((SUM(time_spent) FILTER(WHERE activity_type = 'open'))/(SUM(time_spent) FILTER(WHERE activity_type IN ('send', 'open')))*100.0, 2) as open_perc
FROM joined
GROUP BY age_bucket;
```
![image](https://user-images.githubusercontent.com/50200083/223914789-c71ca0b1-9d6b-425c-8a5a-b84017af6969.png)

### ✏️ Twitter | Tweets' Rolling Averages
[Question: ](https://datalemur.com/questions/rolling-average-tweets) The table below contains information about tweets over a given period of time. Calculate the 3-day rolling average of tweets published by each user for each date that a tweet was posted. Output the user id, tweet date, and rolling averages rounded to 2 decimal places.

**Important Assumptions:**
- Rows in this table are consecutive and ordered by date.
- Each row represents a different day
- A day that does not correspond to a row in this table is not counted. The most recent day is the next row above the current row.

```sql
WITH counts AS (
SELECT user_id, tweet_date, COUNT(*) as tweets_ct
FROM tweets
GROUP BY user_id, tweet_date
ORDER BY user_id)

SELECT user_id, tweet_date, ROUND(AVG(tweets_ct) 
       OVER (PARTITION BY user_id ORDER BY tweet_date ROWS BETWEEN 2 preceding AND current row), 2) as rolling_avg_3days
FROM counts;
```
![image](https://user-images.githubusercontent.com/50200083/224510146-536be118-0397-4cea-ba8d-5548a97e5ecf.png)

### ✏️ Amazon | Highest-Grossing Items
[Question: ](https://datalemur.com/questions/sql-highest-grossing) Assume you are given the table containing information on Amazon customers and their spending on products in various categories.

Identify the top two highest-grossing products within each category in 2022. Output the category, product, and total spend.

```sql
WITH total AS (
  SELECT category, product, SUM(spend) as total_spend, RANK() OVER(PARTITION BY category ORDER BY SUM(spend) DESC)
  FROM product_spend
  WHERE EXTRACT(YEAR FROM transaction_date) = 2022
  GROUP BY category, product
)

SELECT category, product, total_spend
FROM total
WHERE rank < 3;
```
![image](https://user-images.githubusercontent.com/50200083/226251728-cdeda1b6-113e-43d6-b4a1-db455746b346.png)


## Level: Hard



### ✏️ Facebook | Active User Retention
[Question: ](https://datalemur.com/questions/user-retention) Assume you have the table below containing information on Facebook user actions. Write a query to obtain the active user retention in July 2022. Output the month (in numerical format 1, 2, 3) and the number of monthly active users (MAUs).

```sql
with cte as (
SELECT EXTRACT(MONTH FROM event_date) as month, user_id, event_type
FROM user_actions
),
curr as (
SELECT month, user_id
FROM cte b
WHERE month = 7 AND event_type IN ('sign-in', 'like', 'comment')
),
last as (
SELECT a.month, a.user_id
FROM cte a, curr c
WHERE a.month = c.month-1 AND event_type IN ('sign-in', 'like', 'comment')
),
joined as(
SELECT curr.month, curr.user_id
FROM last
JOIN
curr
ON last.user_id = curr.user_id
)
SELECT month, COUNT(DISTINCT user_id)
FROM joined
GROUP BY month;
```
![image](https://user-images.githubusercontent.com/50200083/223934685-56604407-38f7-4216-8cdb-48658b344a80.png)

### ✏️ Wayfair | Y-on-Y Growth Rate
[Question: ](https://datalemur.com/questions/yoy-growth-rate) Assume you are given the table below containing information on user transactions for particular products. Write a query to obtain the year-on-year growth rate for the total spend of each product for each year.

Output the year (in ascending order) partitioned by product id, current year's spend, previous year's spend and year-on-year growth rate (percentage rounded to 2 decimal places).

```sql
with yearly_spending AS (
  SELECT EXTRACT(YEAR FROM transaction_date) as year, product_id, SUM(spend) as curr_year_spend
  FROM user_transactions
  GROUP BY EXTRACT(YEAR FROM transaction_date), product_id
  ORDER BY product_id, year
),
combined AS (
  SELECT *, 
         LAG(curr_year_spend, 1) OVER (PARTITION BY product_id ORDER BY product_id) AS prev_year_spend
  FROM yearly_spending
)

SELECT *, ROUND((curr_year_spend - prev_year_spend)/prev_year_spend*100, 2) AS yoy_rate
FROM combined;

```
![image](https://user-images.githubusercontent.com/50200083/225814784-73c8b2b3-ff18-458a-8a6d-1859d526339d.png)

