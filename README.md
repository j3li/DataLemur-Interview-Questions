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



