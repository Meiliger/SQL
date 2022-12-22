# Interactive SQL simulator

*1. Formulate an SQL query to create the `book` table, enter it in the code window (located below) and send it for verification (the Submit button). Structure of the `book` table:*
```sql
CREATE TABLE book
  (
     book_id INT PRIMARY KEY auto_increment,
     title   VARCHAR(50),
     author  VARCHAR(30),
     price   DECIMAL(8, 2),
     amount  INT
  ); 
```
*2. Enter a new row in the `book` table (text values (VARCHAR type) must be enclosed in either double or single quotes):*
```sql
INSERT INTO book (title, author, price, amount) VALUES ('Мастер и Маргарита', 'Булгаков М.А.', 670.99, 3);
```
*3. Enter the last three records in the `book` table, the first record has already been added in the previous step:*
```sql
INSERT INTO book
            (title,
             author,
             price,
             amount)
VALUES      ('Белая гвардия',
             'Булгаков М.А.',
             540.50,
             5),
            ('Идиот',
             'Достоевский Ф.М.',
             460.00,
             10),
            ('Братья Карамазовы',
             'Достоевский Ф.М.',
             799.01,
             2);
```
*4. Display information about all books stored in the warehouse*
```sql
SELECT *
FROM book; 
```
*5. At the end of the year, the price of all books in stock is recalculated and reduced by 30%. Write an SQL query that selects titles, authors, quantities from the `book` table and calculates new book prices. Name the column with the new price `new_price`, round the price to 2 decimal places*
 ```sql
SELECT title, author, amount, ROUND((price*0.7),2) AS new_price
FROM book;
```
*6. When analyzing book sales, it turned out that the most popular are the books of Mikhail Bulgakov, in second place are the books of Sergei Yesenin. Based on this, they decided to raise the price of Bulgakov's books by 10%, and the price of Yesenin's books - by 5%. Write a query where to include the author, book title and new price, name the last column new_price. Round the value to two decimal places*
 ```sql
SELECT author, title, ROUND(IF(author = "Булгаков М.А.", price * 1.1, IF (author = "Есенин С.А.", price * 1.05, price)), 2) AS new_price
FROM book;
```
*7. Output the title and authors of those books whose prices belong to the interval from 540.50 to 800 (including borders), and the number is either 2, or 3, or 5, or 7*
```sql
SELECT title, author
FROM book
WHERE price BETWEEN 540.50 AND 800 AND amount IN(2, 3, 5, 7);
```
*8. Output the title and author of those books whose title consists of two or more words, and the author's initials contain the letter `C`. Consider that in the title the words are separated from each other by spaces and do not contain punctuation marks, a space is required between the author's surname and initials, initials are written without a space in the format: letter, dot, letter, dot. Sort information alphabetically by book title*
```sql
SELECT title, author
FROM book
WHERE title LIKE '_% _%' AND (author LIKE '_% С._.' OR author LIKE '% _.С.')
ORDER BY title;
```
*9. ВSelect different (unique) elements of the amount column of the `book` table*
```sql
select distinct amount
from book;
```
*10. Display information (author, title and price) about books whose prices are less than or equal to the average price of books in stock. Display information sorted by price in descending order. Calculate the average as the average of the price of the book*
```sql
SELECT author, title, price
FROM book
WHERE price <= (
    SELECT AVG(price)
    FROM book)
ORDER BY price DESC;
```
*11. Display information (author, book, and quantity) about those books whose number of copies in the book table is not duplicated*
```sql
SELECT author, title, amount 
FROM book
WHERE amount IN (
                SELECT amount
                FROM book
                GROUP BY amount
                HAVING COUNT(amount) = 1);
```
*12. Display information about books (author, title, price) whose price is less than the highest of the minimum prices calculated for each author*
```sql
SELECT author, title, price
FROM book
WHERE price < any(
    SELECT MIN(price)
    FROM book
    GROUP BY author
)
```
*13. Enter from the supply table into the `book` table only those books whose authors are not in the `book` table*
```sql
INSERT INTO book (title, author, price, amount) 
SELECT title, author, price, amount 
FROM supply
WHERE author NOT IN (
        SELECT author 
        FROM book
      );
```
*14. Decrease by 10% the price of those books in the `book` table whose number belongs to the interval from 5 to 10, including the borders*
```sql
UPDATE book
SET price = 0.9*price
WHERE amount BETWEEN 5 and 10;
```
*15. In the `book` table, you need to adjust the value for the customer in the buy column so that it does not exceed the number of book copies specified in the amount column. And the price of those books that the buyer did not order, reduce by 10%*
```sql
UPDATE book
SET buy = IF(buy > amount, amount, buy),
    price = IF(buy = 0, price*0.9, price);
```
*16. For those books in the `book` table that are in the `supply` table, not only increase their number in the `book` table (increase their number by the value of the amount column in the `supply` table), but also recalculate their price (for each book, find the sum of prices from the `book` and `supply` tables and divide by 2)*
```sql
UPDATE book, supply 
SET book.amount = book.amount + supply.amount,
book.price = (book.price + supply.price)/2
WHERE book.title = supply.title AND book.author = supply.author;
```
*17. Remove from the `supply` table the books of those authors whose total number of book copies in the `book` table exceeds 10*
```sql
DELETE FROM supply
WHERE author IN (
    SELECT author
    FROM book
    GROUP BY author
    HAVING SUM(book.amount) > 10
);
```
*18. Create an `ordering` table to include the authors and titles of those books whose number of copies in the `book` table is less than the average number of book copies in the `book` table. Include the amount column in the table, in which for all books specify the same value - the average number of copies of books in the book `table`*
```sql
CREATE TABLE ordering AS
  SELECT author,
         title,
         (SELECT Round(Avg(amount))
          FROM   book) AS amount
  FROM   book
  WHERE  amount < (SELECT Round(Avg(amount))
                   FROM   book); 
```
*19. Display from the table data on business trips of those employees, the number of arrivals starting with the letter `a`, sorted in descending order, statistics of the last days of a business trip of the type. The result includes the columns name, city, daily, date_first, date_last.*
```sql
SELECT employee_name, monthly_salary 
FROM employees 
INNER JOIN employees_salary
ON employees.id = employees_salary.employee_id
INNER JOIN roles_employees 
ON employees.id = roles_employees.employee_id
INNER JOIN roles
ON roles.id = roles_employees.role_id
WHERE role_name LIKE '%Senior Java developer%';
```
*20. Display from the table data on business `trips` of those employees, the number of arrivals starting with the letter "a", sorted in descending order, statistics of the last days of a business trip of the type. The result includes the columns `name`, `city`, `daily`, `date_first`, `date_last`.*
```sql
SELECT name, city, per_diem, date_first, date_last
FROM trip
WHERE name LIKE '%а _._.'
ORDER BY date_last desc;
```
*21. For each city, calculate how many times employees have been to it. Display information sorted alphabetically by city name. Name the calculated column `Quantity`.*
```sql
SELECT city, COUNT(city) AS Quantity
FROM trip
GROUP BY city
ORDER BY city;
```
*22. Display the two cities in which employees were most often on business trips. Name the calculated column `Quantity`.*
```sql
SELECT city, COUNT(city) AS Quantity
FROM trip
GROUP BY city
ORDER BY Quantity DESC
LIMIT 2;
```
*23. Display information about the trips of the employee(s) that were the shortest in time. Include the columns `name`, `city`, `date_first`, `date_last` in the result.*
```sql
SELECT name, city, date_first, date_last
FROM trip
WHERE (datediff(date_last, date_first)+1) IN
	(
		SELECT MIN(datediff(date_last, date_first)+1)
		FROM trip
	);
```
*24. Display information about business trips, the beginning and end of which belong to the same month (any year can be). Include the columns `name`, `city`, `date_first`, `date_last` in the result. Sort the rows first in alphabetical order by the name of the city, and then by the last name of the employee.*
```sql
SELECT name, city, date_first, date_last
FROM trip
WHERE (MONTH(date_first) = MONTH(date_last))
ORDER BY city, name;
```
*25. Rewrite the query to create the book table to match the structure shown in the logic diagram:*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/pic.png)
</details>

```sql
CREATE TABLE book (
    book_id INT PRIMARY KEY AUTO_INCREMENT, 
    title VARCHAR(50), 
    author_id INT NOT NULL,
    genre_id int,
    price DECIMAL(8,2), 
    amount INT, 
    FOREIGN KEY (author_id)  REFERENCES author (author_id),
    FOREIGN KEY (genre_id)  REFERENCES genre (genre_id)
);
```
*26. Display the title, genre and price of those books, the number of which is more than 8, sorted in descending order of price*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/pic.png)
</details>

```sql
SELECT title, name_genre, price
FROM book
JOIN genre
ON book.genre_id = genre.genre_id
WHERE amount > 8
ORDER BY price DESC;
```
*27. Display all genres that are not represented in the books in stock*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/pic.png)
</details>

```sql
SELECT name_genre
FROM genre
LEFT JOIN book b
ON genre.genre_id = b.genre_id
WHERE title IS NULL;
```
*28. Display information about books (genre, book, author) belonging to the genre that includes the word "novel" sorted by book titles*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/pic.png)
</details>

```sql
SELECT name_genre, title, name_author
FROM genre
JOIN book
ON genre.genre_id = book.genre_id
JOIN author a
ON a.author_id = book.author_id
WHERE name_genre = 'novel'
ORDER BY title;
```
*29. Count the number of copies of each author's books from the author table. Output those authors whose number of books is less than 10, sorted in ascending order of number. Name the last column `Quantity`*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/pic.png)
</details>

```sql
SELECT name_author, SUM(amount) AS Quantity
FROM author
LEFT JOIN book
ON author.author_id = book.author_id
GROUP BY name_author
HAVING Quantity < 10 OR COUNT(title) = 0
ORDER BY Quantity;
```
*30. Display information about books (book title, author's surname and initials, genre name, price and number of book copies) written in the most popular genres, sorted alphabetically by book title. The most popular is the genre, the total number of book copies of which in stock is maximum*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/pic.png)
</details>

```sql
SELECT  title, name_author, name_genre, price, amount
FROM author
    INNER JOIN book ON author.author_id = book.author_id
    INNER JOIN genre ON  book.genre_id = genre.genre_id
WHERE genre.genre_id IN
         (
          SELECT query_in_1.genre_id
          FROM
              (
                SELECT genre_id, SUM(amount) AS sum_amount
                FROM book
                GROUP BY genre_id
               )query_in_1
          INNER JOIN
              (
                SELECT genre_id, SUM(amount) AS sum_amount
                FROM book
                GROUP BY genre_id
                ORDER BY sum_amount DESC
                LIMIT 1
               ) query_in_2
          ON query_in_1.sum_amount= query_in_2.sum_amount
         )
         ORDER BY title asc;  
```
*31. If there are identical books in the `supply` and `book` tables that have the same price, display their title and author, and also calculate the total number of copies of books in the `supply` and `book` tables, name the columns `Title`, `Author` and `Quantity`*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/pic2.png)
</details>

```sql
SELECT book.title AS Title, name_author AS Author,  book.amount + supply.amount AS Quantity
FROM author
JOIN book
USING(author_id)
JOIN supply
ON book.title = supply.title AND
   supply.price = book.price;
```
*32. Calculate how many times each book was ordered, display its author for the book (you need to calculate how many orders each book appears in). Print the last name and initials of the author, the title of the book, name the last column `Quantity`. The result is sorted first by the names of the authors, and then by the titles of the books*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/%D1%81.jpg)
</details>

```sql
SELECT author.name_author, book.title, COUNT(buy_book.book_id) AS Quantity
FROM author
JOIN book ON author.author_id = book.author_id
LEFT JOIN buy_book ON book.book_id = buy_book.book_id
GROUP BY author.name_author, book.title
ORDER BY author.name_author, book.title;
```
*33. Display the cities where customers who placed orders in the online store live. Specify the number of orders to each city, name this column `Quantity`. Display information in descending order by the number of orders, and then in alphabetical order by city name*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/e.png)
</details>

```sql
SELECT city.name_city, COUNT(city.city_id) AS Quantity
FROM city
JOIN client ON city.city_id = client.city_id
JOIN buy ON client.client_id = buy.client_id
GROUP BY city.name_city
ORDER BY Quantity DESC, city.name_city;
```
*34. Display the numbers of all paid orders and the dates when they were paid*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/f.png)
</details>

```sql
SELECT buy_step.buy_id, buy_step.date_step_end
FROM step
JOIN buy_step
ON step.step_id = buy_step.step_id
WHERE buy_step.date_step_end IS NOT NULL AND step.step_id = 1;
```
*35. Display information about each order: its number, who formed it (user's last name) and its cost (the sum of the products of the number of ordered books and their prices), sorted by order number. The last column is called `Cost`*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/g.png)
</details>

```sql
SELECT buy.buy_id, client.name_client, SUM(buy_book.amount * book.price) AS Cost
FROM client
JOIN buy
ON client.client_id = buy.client_id
JOIN buy_book
ON buy.buy_id = buy_book.buy_id
JOIN book
ON buy_book.book_id = book.book_id
GROUP BY buy.buy_id, client.name_client
ORDER BY buy.buy_id;
```
*36. Select all clients who ordered Dostoevsky's books, display the information sorted alphabetically. In the solution, use the author's last name, not his id*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/h.png)
</details>

```sql
SELECT DISTINCT c.name_client
FROM client c
JOIN buy ON c.client_id = buy.client_id
JOIN buy_book bb ON buy.buy_id = bb.buy_id
JOIN book b ON bb.book_id = b.book_id
JOIN author a ON b.author_id = a.author_id
WHERE a.name_author = 'Dostoevsky'
ORDER BY c.name_client;
```
*37. Output the genre (or genres) in which the most copies of books were ordered, indicate this number. Name the last column `Quantity`*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/r.png)
</details>

```sql
SELECT genre.name_genre,
       (sum(bb.amount)) AS Quantity
FROM genre
JOIN book b ON genre.genre_id = b.genre_id
JOIN buy_book bb ON b.book_id = bb.book_id
GROUP BY genre.name_genre
HAVING SUM(bb.amount) =
  (SELECT MAX(сумма)
   FROM
     (SELECT SUM(bb.amount) AS Total
      FROM genre
      JOIN book b ON genre.genre_id = b.genre_id
      JOIN buy_book bb ON b.book_id = bb.book_id
      GROUP BY genre.name_genre) AS query_in); 
```
*38. The number of those books in stock that were included in order number 5 should be reduced by the quantity indicated in order number 5*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/1.png)
</details>

```sql
UPDATE book, buy_book
SET book.amount = book.amount - buy_book.amount
WHERE buy_book.buy_id = 5 AND book.book_id = buy_book.book_id;
```
*39. Create a general invoice (table `buy_pay`) for order number 5. Where to include the order number, the number of books in the order (column name `Quantity`) and its total cost (column name `Total`)*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/2.png)
</details>

```sql
CREATE TABLE buy_pay AS
SELECT buy_book.buy_id,
       SUM(buy_book.amount) AS Quantity,
       SUM(buy_book.amount * book.price) AS Total
FROM book
JOIN buy_book USING (book_id)
WHERE buy_id = 5; 
```
*40. In the `buy_step` table for order number 5, include all the steps from the `step` table that this order must go through. Enter `Null` in the `date_step_beg` and `date_step_end` columns of all records*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/3.png)
</details>

```sql
INSERT INTO buy_step(buy_id, step_id)
SELECT 5 AS buy_id,
       step_id
FROM step; 
```
*41. Display the students who passed the discipline "Fundamentals of Databases", indicate the date of the attempt and the result. Display information in descending order of test results*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/4.png)
</details>

```sql
SELECT name_student,
       date_attempt,
       result
FROM student
JOIN attempt USING (student_id)
JOIN subject USING (subject_id)
WHERE name_subject = 'Fundamentals of Databases'
ORDER BY result DESC; 
```
*42. Output how many attempts the students made in each discipline, as well as the average result of the attempts, which should be rounded up to 2 decimal places. The result of an attempt is understood as the percentage of correct answers to the test questions, which is listed in the `result` column. The result includes the name of the discipline, as well as the calculated columns `Quantity` and `Average`. Display information in descending order of average results*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/5.png)
</details>

```sql
SELECT name_subject,
       COUNT(attempt_id) AS Quantity,
       ROUND(AVG(result), 2) AS Average
FROM subject
LEFT JOIN attempt USING (subject_id)
GROUP BY name_subject; 
```
*43. Output the students (various students) with the maximum results of attempts. The information is sorted alphabetically by student's last name*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/6.png)
</details>

```sql
SELECT name_student, result
FROM student
JOIN attempt USING (student_id)
WHERE result IN
    (SELECT MAX(result)
     FROM attempt)
ORDER BY name_student; 
```
*44. If the student made several attempts in the same discipline, then display the difference in days between the first and last attempt. In the result, include the student's first and last name, the name of the discipline, and the calculated column `Interval`. Display information in ascending order. Students who have made one attempt in the discipline are not taken into account*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/7.png)
</details>

```sql
SELECT name_student,
       name_subject,
       DATEDIFF(MAX(date_attempt), MIN(date_attempt)) AS Interval
FROM student
JOIN attempt USING (student_id)
JOIN subject USING (subject_id)
GROUP BY name_student,
         name_subject
HAVING COUNT(attempt_id) > 1
ORDER BY Interval; 
```
*45. Students can be tested in one or more disciplines (not necessarily in all). Display the discipline and the number of unique students (name the column `Quantity`) who were tested on it. Sort the information first in descending order of quantity, and then by the name of the discipline. The result also includes disciplines for which students have not yet been tested, in this case, indicate the number of students 0*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/7.png)
</details>

```sql
SELECT name_subject,
       COUNT(DISTINCT(student_id)) AS Quantity
FROM subject
LEFT JOIN attempt USING (subject_id)
GROUP BY name_subject
ORDER BY Quantity DESC,
         name_subject; 
```
*46. Randomly select 3 questions in the discipline "Database Fundamentals". Include the `question_id` and `name_question columns` in the result*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/9.png)
</details>

```sql
SELECT question_id, name_question
FROM subject
JOIN question ISING (subject_id)
WHERE name_subject = 'Database Fundamentals'
ORDER BY RAND()
LIMIT 3 
```
*47. Calculate test results. The result of the attempt is calculated as the number of correct answers divided by 3 (the number of questions in each attempt) and multiplied by 100. Round the result to two decimal places. Display the student's last name, subject name, date and result. Name the last column `Result`. Sort the information first by the last name of the student, then in descending order by the date of the attempt*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/10.png)
</details>

```sql
SELECT name_student,
       name_subject,
       date_attempt,
       ROUND(((SUM(is_correct)/3)*100),2) AS Result
FROM student s
JOIN attempt a ON s.student_id = a.student_id
JOIN subject s2 ON a.subject_id = s2.subject_id
JOIN testing t ON a.attempt_id = t.attempt_id
JOIN answer a2 ON t.answer_id = a2.answer_id
GROUP BY name_student,
         name_subject,
         date_attempt
ORDER BY name_student,
         date_attempt DESC; 
```
*48. Randomly select three questions for the discipline, in which the last student listed in the `attempt` table is going to be tested, and add them to the `testing` table. `id` of the last attempt to get as the maximum `id` value from the `attempt` table*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/11.png)
</details>

```sql
INSERT INTO testing (attempt_id, question_id)
SELECT attempt_id, question_id
FROM question
INNER JOIN attempt USING (subject_id)
WHERE attempt_id = (SELECT MAX(attempt_id) FROM attempt)
ORDER BY RAND()
LIMIT 3; 
```
*49. Display applicants who want to enroll in the educational program "Mechatronics and Robotics" sorted by last name*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/12.png)
</details>

```sql
SELECT name_enrollee
FROM enrollee
JOIN program_enrollee USING (enrollee_id)
JOIN program USING (program_id)
WHERE name_program = 'Mechatronics and Robotics'
ORDER BY 1; 
```
*50. Display educational programs for which the subject "Informatics" is required for admission. Programs are sorted in reverse alphabetical order*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/13.png)
</details>

```sql
SELECT name_program
FROM program
JOIN program_subject USING (program_id)
JOIN subject s ON program_subject.subject_id = s.subject_id
WHERE name_subject = 'Informatics'
ORDER BY 1 DESC; 
```
*51. Output the number of applicants who passed the exam in each subject, the maximum, minimum and average scores for the exam subject. Name the calculated columns `Quantity`, `Maximum`, `Minimum`, `Average`. Sort the information by subject name in alphabetical order, round the average value to one decimal place*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/14.png)
</details>

```sql
SELECT name_subject,
       COUNT(subject_id) AS Quantity,
       MAX(result) AS Maximum,
       MIN(result) AS Minimum,
       ROUND(AVG(result), 1) AS Average
FROM subject
JOIN enrollee_subject USING (subject_id)
GROUP BY name_subject
ORDER BY name_subject; 
```
*52. Display educational programs for which the minimum exam score in each subject is greater than or equal to 40 points. Output the programs sorted alphabetically*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/15.png)
</details>

```sql
SELECT name_program
FROM program
JOIN program_subject
ON program.program_id = program_subject.program_id
GROUP BY name_program
HAVING MIN(min_result) >= 40
ORDER BY name_program; 
```
*53. Output educational programs that have the largest recruitment plan, along with this value*
```sql
SELECT name_program, plan
FROM program
WHERE plan =
    (SELECT MAX(plan)
     FROM program); 
```
*54. Calculate how many additional points each applicant will receive. Name the column with extra points Bonus. Display information sorted by last name*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/17.png)
</details>

```sql
SELECT name_enrollee,
       IF(SUM(bonus) IS NULL, 0, SUM(bonus)) AS Bonus
FROM enrollee
LEFT JOIN enrollee_achievement USING (enrollee_id)
LEFT JOIN achievement USING (achievement_id)
GROUP BY name_enrollee
ORDER BY name_enrollee; 
```
*55. Print how many people have applied for each educational program and the competition for it (the number of applications submitted divided by the number of places according to the plan), rounded up to 2 decimal places. In the query, display the name of the faculty to which the educational program belongs, the name of the educational program, the enrollment plan for applicants for the educational program (`plan`), the number of applications submitted (`Quantity`) and the `Competition`. Sort information in descending order*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/17.png)
</details>

```sql
SELECT name_department,
       name_program,
       plan,
       COUNT(enrollee_id) AS Quantity,
       ROUND((COUNT(enrollee_id)/plan),2) AS Competition
FROM program
JOIN department USING (department_id)
JOIN program_enrollee USING (program_id)
GROUP BY name_department,
         name_program,
         plan
ORDER BY Competition DESC; 
```
*56. Display the educational programs for which the subject "Informatics" and "Mathematics" are required for admission in the form sorted by the name of the programs*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/18.png)
</details>

```sql
SELECT name_program
FROM program
JOIN program_subject USING (program_id)
JOIN subject USING (subject_id)
WHERE name_subject IN ('Informatics',
                       'Mathematics')
GROUP BY name_program
HAVING COUNT(name_subject) = 2
ORDER BY name_program; 
```
*57. Calculate the number of points of each applicant for each educational program for which he applied, according to the results of the `exam`. In the result, include the name of the educational program, the surname and name of the applicant, as well as a column with the sum of points, which should be called `itog`. Display the information sorted first by the educational program, and then in descending order of the amount of points*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](18.png)
</details>

```sql
SELECT name_program,
       name_enrollee,
       SUM(result) AS itog
FROM enrollee
JOIN program_enrollee USING (enrollee_id)
JOIN program USING (program_id)
JOIN program_subject ON program.program_id = program_subject.program_id
JOIN enrollee_subject ON enrollee.enrollee_id = enrollee_subject.enrollee_id
AND program_subject.subject_id = enrollee_subject.subject_id
GROUP BY name_program,
         name_enrollee
ORDER BY name_program,
         itog DESC; 
```
*58. Create an auxiliary table `applicant`, where to include the `id` of the educational program, the `id` of the applicant, the sum of the scores of the applicants (column `itog`) in the form sorted first by the `id` of the educational program, and then in descending order of the sum of points*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/19.png)
</details>

```sql
CREATE TABLE applicant AS
SELECT program.program_id,
       enrollee.enrollee_id,
       SUM(result) AS itog
FROM program
JOIN program_subject ON program.program_id = program_subject.program_id
JOIN program_enrollee ON program.program_id = program_enrollee.program_id
JOIN enrollee ON program_enrollee.enrollee_id = enrollee.enrollee_id
JOIN enrollee_subject ON enrollee.enrollee_id = enrollee_subject.enrollee_id
AND program_subject.subject_id = enrollee_subject.subject_id
GROUP BY program_id,
         enrollee_id
ORDER BY 1,
         itog DESC; 
```
*59. Delete records from the applicant table created in the previous step if the `applicant` for the selected educational program did not score the minimum score in at least one subject*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/19.png)
</details>

```sql
DELETE applicant
FROM applicant
JOIN program_subject USING(program_id)
JOIN enrollee_subject USING(enrollee_id, subject_id)
WHERE result < min_ball;

SELECT * FROM applicant; 
```
*60. Increase the total scores of applicants in the `applicant` table by the values of additional scores*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/20.png)
</details>

```sql
UPDATE applicant AS applicant
JOIN
  (SELECT enrollee_id,
          IF(SUM(bonus) IS NULL, 0, SUM(bonus)) AS Reward
   FROM enrollee
   LEFT JOIN enrollee_achievement USING (enrollee_id)
   LEFT JOIN achievement USING (achievement_id)
   GROUP BY enrollee_id
   ORDER BY enrollee_id) bonus ON applicant.enrollee_id = bonus.enrollee_id
SET itog = itog + bonus.Reward; 
```
*61. Since when adding additional points, applicants for each educational program may not follow in descending order of total points, it is necessary to create a new `applicant_order` table based on the `applicant` table. When creating a table, the data must be sorted first by the `id` of the educational program, then in descending order by the final score. And the `applicant` table, which was created as an auxiliary table, must be deleted*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/22.png)
</details>

```sql
CREATE TABLE applicant_order AS
SELECT program_id,
       enrollee_id,
       itog
FROM applicant
ORDER BY 1, 3 DESC;

DROP TABLE applicant;

SELECT *
FROM applicant_order; 
```
*62. Include a new integer type `str_id` column in the `applicant_order` table, position it before the first one*
```sql
ALTER TABLE applicant_order ADD str_id int FIRST;

SELECT *
FROM applicant_order; 
```
*63. Create a `student` table to include applicants who can be recommended for enrollment in accordance with the recruitment plan. Sort the information first in alphabetical order by the name of the programs, and then in descending order of the final score*

<details>
  <summary>Logic diagram</summary>
  
  ![SQL](https://github.com/Meiliger/SQL/blob/main/Interactive%20SQL%20simulator/Schemes/21.png)
</details>

```sql
CREATE TABLE student AS
SELECT name_program,
       name_enrollee,
       itog
FROM program
JOIN applicant_order ON program.program_id = applicant_order.program_id
JOIN enrollee ON applicant_order.enrollee_id = enrollee.enrollee_id
WHERE str_id <= program.plan
ORDER BY 1, 3 DESC;

SELECT *
FROM student; 
```

