# DataLemur Interview Questions
[DataLemur](https://datalemur.com) is a data science platform with real interview questions from top companies, including FAANG companies.

I will be sharing my answers to SQL problems.

### Table of Contents

***

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


