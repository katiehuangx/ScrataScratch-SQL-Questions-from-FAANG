# ScrataScratch

Solutions to ScrataScratch's FAANG SQL interview questions.

## 🟠 EASY
## [Dropbox: Salaries Differences](https://platform.stratascratch.com/coding/10308-salaries-differences?python=)
**Write a query that calculates the difference between the highest salaries found in the marketing and engineering departments. Output just the difference in salaries.**

```sql
-- Method 1
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

```sql
-- Method 2
SELECT
  MAX(CASE WHEN d.department = 'marketing' THEN salary END) -
  MAX(CASE WHEN d.department = 'engineering' THEN salary END) AS salary_diff
FROM db_employee AS e
INNER JOIN db_dept AS d
  ON e.department_id = d.id;
```

<kbd><img width="175" alt="image" src="https://user-images.githubusercontent.com/81607668/139520220-8cbaef69-8dfa-4402-8dd6-0efad77e44e3.png"></kbd>

## [Airbnb: Number Of Bathrooms And Bedrooms](https://platform.stratascratch.com/coding/9622-number-of-bathrooms-and-bedrooms?python=)
**Find the average number of bathrooms and bedrooms for each city’s property types. Output the result along with the city name and the property type.**

```sql
SELECT
  city, property_type, 
  AVG(bathrooms) AS avg_bathroom, 
  AVG(bedrooms) AS avg_bedroom
FROM airbnb_search_details
GROUP BY city, property_type;
```

_Screenshot showing first 5 records only._

<kbd><img width="617" alt="image" src="https://user-images.githubusercontent.com/81607668/139521181-c5aaba65-d83f-4ccd-8577-4ee8e11e627a.png"></kbd>


***

## 🔵 MEDIUM

## [Facebook: Highest Energy Consumption](https://platform.stratascratch.com/coding/10064-highest-energy-consumption?python=)
**Find the date with the highest total energy consumption from the Facebook data centers. Output the date along with the total energy consumption across all data centers.**

```sql
-- date | total_consumption
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

<kbd><img width="614" alt="image" src="https://user-images.githubusercontent.com/81607668/139520290-4c63f55d-3236-4ad6-a99c-fa4ec08ba3fa.png"></kbd>

## [Amazon: Finding User Purchases](https://platform.stratascratch.com/coding/10322-finding-user-purchases?python=)
**Write a query that'll identify returning active users. A returning active user is a user that has made a second purchase within 7 days of any other of their purchases. Output a list of user_ids of these returning active users.**

```sql
WITH next_purchase AS (
SELECT 
  id, user_id, created_at,
  LEAD(created_at) OVER (PARTITION BY user_id ORDER BY created_at) AS next_date
FROM amazon_transactions
),
GAP AS (
SELECT 
  user_id, created_at, next_date,
  (next_date - created_at) AS purchase_gap
FROM next_purchase
)

SELECT DISTINCT user_id
FROM gap
WHERE purchase_gap <= 7;
```

_Screenshot showing first 5 records only_

<kbd><img width="271" alt="image" src="https://user-images.githubusercontent.com/81607668/139520528-7ab76478-6f3e-42e5-b633-b16af8fca4de.png"></kbd>

## [Twitter: Highest Salary In Department](https://platform.stratascratch.com/coding/9897-highest-salary-in-department?python=)
**Find the employee with the highest salary per department. Output the department name, employee's first name along with the corresponding salary.**

```sql
WITH max_salary AS (
SELECT 
  department, 
  MAX(salary) AS max_salary,
  ROW_NUMBER() OVER (PARTITION BY department) AS rank
FROM employee
GROUP BY department
)

SELECT 
  s.department, 
  e.first_name AS employee_name, 
  s.max_salary
FROM employee e
INNER JOIN max_salary s
  ON e.salary = s.salary;
```

<kbd><img width="616" alt="image" src="https://user-images.githubusercontent.com/81607668/139520725-cba359cd-c654-47fa-a179-09667760efaf.png"></kbd>

***

## 🔴 HARD

## [Amazon: Highest Cost Orders](https://platform.stratascratch.com/coding/9915-highest-cost-orders?python=)
**Find the customer with the highest total order cost between 2019-02-01 to 2019-05-01. If customer had more than one order on a certain day, sum the order costs on daily basis. Output their first name, total cost of their items, and the date. For simplicity, you can assume that every first name in the dataset is unique.**

```sql
SELECT
  first_name,
  SUM(total_order_cost) OVER (
    PARTITION BY first_name, order_date) AS total_costs,
  order_date
FROM customers AS c
INNER JOIN orders AS o
  ON c.id = o.cust_id
GROUP BY first_name, total_order_cost, order_date
HAVING order_date BETWEEN '2019-02-01' and '2019-05-01'
ORDER BY total_costs DESC
LIMIT 1;
```

<kbd><img width="617" alt="image" src="https://user-images.githubusercontent.com/81607668/139520598-af1404b5-5325-44e5-8c9e-3455f3383859.png"></kbd>

## [Top 5 States With 5 Star Businesses](https://platform.stratascratch.com/coding/10046-top-5-states-with-5-star-businesses?python=)
**Find the top 5 states with the most 5 star businesses. Output the state name along with the number of 5-star businesses and order records by the number of 5-star businesses in descending order. In case there are ties in the number of businesses, return all the unique states. If two states have the same result, sort them in alphabetical order.**

```sql
WITH ranked_cte AS (
SELECT
  state,
  COUNT(business_id) AS business_id_count,
  DENSE_RANK() OVER (
    PARTITION BY state 
    ORDER BY COUNT(business_id) DESC) AS ranking
FROM yelp_business
WHERE stars = 5
GROUP BY state
)

SELECT 
  state, business_id_count
FROM ranked_cte
ORDER BY business_id_count DESC
LIMIT 6;
```

<kbd><img width="447" alt="image" src="https://user-images.githubusercontent.com/81607668/139520678-06f62ed9-dc1f-4ebb-949b-623e94e33591.png"></kbd>




