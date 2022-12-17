# SQL Mate Academy

*1. Find all movies*
```sql
SELECT *
FROM movie;
```
*2. Find all movies in English*
```sql
SELECT *
FROM movie
WHERE language = 'English';
```
*3. Find all movies except 2019 movies*
```sql
SELECT *
FROM movie
WHERE year != 2019;
```
*4. Movies released after 2017*
```sql
SELECT *
FROM movie
WHERE year > 2017;
```
*5. All movies that follow the letter "L" in alphabetical order are displayed, including LADY BIRD*
```sql
SELECT *
FROM movie
WHERE title >= 'LADY BIRD';
```
*6. Find all movies released in 2017 in the USA*
```sql
SELECT *
FROM movie
WHERE year = 2017 AND release_country = 'USA';
```
*7. Display all information about male staff who started working after 01.01.2014 and have a salary of more than 10,000*
```sql
SELECT *
FROM staff
WHERE sex = 'male' AND date_of_hiring > '2014-01-01' AND salary > 10000;
```
*8. The titles of all books published up to and including 2012 or after 2017 and over 240 pages must be displayed*
```sql
SELECT title
FROM book
WHERE (publish_year <= 2012 OR publish_year >= 2017) AND number_of_pages > 240;
```
*9. Find all films where there is no information about their budget*
```sql
SELECT *
FROM movie
WHERE budget IS NULL;
```
*10. Find all movies starting with "L"*
```sql
SELECT *
FROM movie
WHERE title = 'L';
```
*11. Find all movies with at least one letter "L" in the title*
```sql
SELECT *
FROM movie
WHERE title LIKE '%L%';
```
*12. Find movies whose title contains the letter "A" in the second position*
```sql
SELECT *
FROM movie
WHERE title LIKE '_A%';
```
*13. Find all movies released in 2019 from June to November inclusive*
```sql
SELECT *
FROM movie
WHERE data_release BETWEEN '2019-06-01' AND '2019-11-30';
```
*14. Find movies released in 2015 or 2017 or 2019*
```sql
SELECT *
FROM movie
WHERE year IN(2015, 2017, 2019);
```
*15. Sort movies from newest to oldest so that movie titles are displayed in alphabetical order*
```sql
SELECT *
FROM movie
ORDER BY year DESC, title ASC;
```
*16. Find the first name, last name, and date of birth of the oldest employee still working at the job*
```sql
SELECT first_name, last_name, birth_date
FROM staff
WHERE date_of_dismissal IS NULL
ORDER BY birth_date
LIMIT 1;
```
*17. Find how many calories are in the least caloric dish*
```sql
SELECT MIN(calories) AS calories
FROM dish;
```
*18. Find how many calories a person will get if he eats three meals: "Baked potato", "Crumble", "Meatball"*
```sql
SELECT SUM(calories) AS total_calories
FROM dish
WHERE name IN('Baked potato', 'Crumble', 'Meatball');
```
*19. How many pages were printed each year from 2011 to 2015 inclusive, sort the result by year in reverse order*
```sql
SELECT publish_year AS year, SUM(number_of_pages) AS number_of_pages
FROM book
WHERE publish_year BETWEEN 2011 AND 2015
GROUP BY publish_year
ORDER BY publish_year desc;
```
*20. Find the total number of all products in the "Seafood" category*
```sql
SELECT SUM(amount) AS total_amount
FROM product
INNER JOIN category
ON product.category_id = category.id
WHERE category.name = 'Seafood';
```
*21. Find those employees whose name contains the letter "n"*
```sql
SELECT first_name, last_name, name
FROM employee
INNER JOIN department
ON employee.department_id = department.id
WHERE last_name LIKE '%n%';
```
*22. Find the cheapest products in each category*
```sql
SELECT category.name AS category_name, MIN(price) AS min_price
FROm product
INNER JOIN category
ON product.category_id = category.id
GROUP BY category.name;
```
*23. Find the number of products belonging to the category "Meat" or "Grains"*
```sql
SELECT category.name AS category_name, COUNT(amount) AS number_of_products
FROM product
INNER JOIN category
ON product.category_id = category.id
WHERE category.name = 'Meat' OR category.name = 'Grains'
GROUP BY category.name;
```
*24. Find the name of the department, the average salary, and the number of employees working in each department*
```sql
SELECT name AS department_name, AVG(salary) AS average_salary, COUNT(*) AS number_of_employees
FROM employee
INNER JOIN department
ON employee.department_id = department.id
GROUP BY name;
```
*25. Find the average grade that students, born after 01.01.2005, get each year in each subject. Sort subjects alphabetically, and years in descending order*
```sql
SELECT subject AS subject, year AS year, AVG(mark) AS average_mark
FROM scoreboard
INNER JOIN pupil
ON scoreboard.pupil_id = pupil.id
WHERE birth_date > '2005-01-01'
GROUP BY subject, year
ORDER BY by subject, year desc;
```
*26. Find all departments including those with no employees*
```sql
SELECT name AS department_name, first_name AS first_name, last_name AS last_name
FROM employee
RIGHT JOIN department
ON employee.department_id = department.id;
```
*27. Find information about all existing dishes in the format [dish name]: [price] UAH*
```sql
SELECT CONCAT(name,':', ' ', price, ' UAH') AS dish_information
FROM dish;
```
*28. Find the number of new employees who were hired each year*
```sql
SELECT EXTRACT(year FROM date_of_hiring) AS year, COUNT(*) AS number_of_staff
FROM staff
GROUP BY year;
```
*29. Find how many students have a birthday in the current calendar month*
```sql
SELECT CONCAT(*) AS number_of_pupils
FROM pupil
WHERE EXTRACT(month FROM birth_date) = EXTRACT(month FROM current_date);
```
*30. Find information about each of the products in the "Beverages" category in the form: [product name] - [amount] units*
```sql
SELECT CONCAT(product.name, ' - ', amount, ' units') AS product_information
FROM category
INNER JOIN product
ON category.id = product.category_id
WHERE category.name = 'Beverages';
```
*31. Find information about the GPA in each subject for the student with id 3. The result should be presented in the column named pupil_mark in the following form: [first_name] [last_name]: [subject] - [average mark]*
```sql
SELECT CONCAT(first_name, ' ', last_name, ': ', subject, ' - ', AVG(mark)) AS pupil_mark
FROM pupil
INNER JOIN scoreboard
ON pupil.id = scoreboard.pupil_id
WHERE pupil.id = 3
GROUP BY subject;
```
*32. Find the average salary for each position of employees still working at the job. Enter the result only if the average value of the salary is more than 10,000*
```sql
SELECT position, AVG(salary) AS average_salary
FROM staff
WHERE date_of_dismissal IS NULL
GROUP BY position
HAVING average_salary > 10000;
```
*33. Find categories that have four or more unique product names*
```sql
SELECT category.name AS category_name, COUNT(product.name) AS number_of_products
FROM category
INNER JOIN product
ON category.id = product.category_id
GROUP BY category_name
HAVING number_of_products >= 4;
```
*34. Find the name of the category in which the total number of all products is more than 50*
```sql
SELECT category.name AS category_name, SUM(product.amount) AS total_amount
FROM category
INNER JOIN product
ON category.id = product.category_id
GROUP BY category_name
HAVING total_amount > 80;
```
*35. Find the average grades in each subject for all years of the student 'Eugen Tsven'. The results are displayed only if the average score is 10 or more*
```sql
SELECT subject, AVG(mark) AS average_mark
FROM pupil
INNER JOIN scoreboard
ON pupil.id = scoreboard.pupil_id
WHERE first_name = 'Eugen' AND last_name = 'Tsven'
GROUP BY subject
HAVING average_mark >= 10;
```
*36. Find the name of the departments and the total amount of money spent on employee salaries. Show only those departments with more than three employees. Sort the result by amount of money in descending order*
```sql
SELECT department.name AS department_name, SUM(salary) AS amount_of_money
FROM department
INNER JOIN employee
ON department.id = employee.department_id 
GROUP BY department_name
HAVING COUNT(*) > 3
ORDER BY amount_of_money DESC;
```
*37. Find information about the number of employees who were hired each month. Show the result only if the number of such employees is 3 or more*
```sql
SELECT EXTRACT(month FROM hiring_date) AS month, COUNT(*) AS number_of_employees
FROM employee
GROUP BY month
HAVING number_of_employees >= 3;
```
*38. Find the name of the category and the price of the most expensive product from this category, only if the category has 3 or more unique products. Sort the result by price in ascending order*
```sql
SELECT category.name AS category_name, MAX(price) AS max_price
FROM product
INNER JOIN category
ON category.id = product.category_id
GROUP BY category_name
HAVING COUNT(product.id) >= 3
ORDER BY MAX(price);
```
*39. Find category names for products under 300*
```sql
SELECT distinct category.name
FROM product
INNER JOIN category
ON product.category_id = category.id
WHERE price < 300;
```
*40. Find the number of orders placed by each customer from London*
```sql
SELECT orders.customer_id AS customer_id, COUNT(*) AS orders
FROM customers
INNER JOIN orders
ON customers.customer_id = orders.customer_id
WHERE city = 'London'
GROUP BY customer_id;
```


