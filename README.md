# ScrataScratch

Solutions to ScrataScratch's FAANG SQL interview questions

## [Dropbox: Salaries Differences](https://platform.stratascratch.com/coding/10308-salaries-differences?python=)
**Write a query that calculates the difference between the highest salaries found in the marketing and engineering departments. Output just the difference in salaries.**

```sql
SELECT MAX(e.salary) - 
  (SELECT MAX(salary) 
  FROM db_employee AS e
  INNER JOIN db_dept AS d
    ON e.department_id = d.id
  WHERE d.department = 'engineering')
FROM db_employee AS e
INNER JOIN db_dept AS d
  ON e.department_id = d.id
WHERE d.department = 'marketing';
```

<img width="175" alt="image" src="https://user-images.githubusercontent.com/81607668/139520220-8cbaef69-8dfa-4402-8dd6-0efad77e44e3.png">

## [Facebook: Highest Energy Consumption](https://platform.stratascratch.com/coding/10064-highest-energy-consumption?python=)
**Find the date with the highest total energy consumption from the Facebook data centers. Output the date along with the total energy consumption across all data centers.**

```sql
WITH data_cte AS 
(
SELECT date, consumption, 'EU' AS center
FROM fb_eu_energy
UNION ALL
SELECT date, consumption, 'Asia' AS center
FROM fb_eu_energy
UNION ALL
SELECT date, consumption, 'NA' AS center
FROM fb_eu_energy
)

SELECT date, SUM(consumption) AS total_energy
FROM data_cte
GROUP BY date
ORDER BY total_energy DESC
LIMIT 2;
```

<img width="614" alt="image" src="https://user-images.githubusercontent.com/81607668/139520290-4c63f55d-3236-4ad6-a99c-fa4ec08ba3fa.png">

## [Amazon: Finding User Purchases](https://platform.stratascratch.com/coding/10322-finding-user-purchases?python=)
**Write a query that'll identify returning active users. A returning active user is a user that has made a second purchase within 7 days of any other of their purchases. Output a list of user_ids of these returning active users.**
