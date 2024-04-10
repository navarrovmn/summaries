* This summary assumes PostgresQL;

# Types of Functions
___
* **Aggregate**: operates on all rows of the database to do some processing and return a result;
	* AVG, COUNT, MIN, MAX, SUM
```SQL
 -- counting the entries for a table
 SELECT count(column) FROM table;
```

* **Scalar***: run against each row to produce multiple outputs (like concat);
```SQL
-- can concatenate txt too, not only columns
SELECT CONCAT(column, " ", other_column) FROM table;
```

# Filtering
___
* OR creates a new filtering while AND appends to a filter. Use parenthesis to separate filters;
* `coalesce` function provides a default for NULL values;
```SQL
SELECT coalesce(column, 'default value') AS column_alias FROM table;
```
* `BETWEEN AND` can be used to do an inclusive range search between two values:
```SQL
SELECT column
FROM table
WHERE column BETWEEN X AND Y;
```
* `IN` matches a value in a list of values:
```SQL
SELECT *
FROM table
WHERE column IN (value1, value2, value3, ...);
```
* `DISTINCT` removes duplicates;
```SQL
-- One column or more
SELECT
  DISTINCT column1, column2
FROM
  table;
```
### Like
* Use it when you don't exactly know what you are looking for;
```SQL
-- Will return all employees whose name start with an M
SELECT *
FROM employees
WHERE first_name LIKE 'M%';
```
* Use *patterns* to get the data:
![[patterns.png]]
* Postgres only does the comparison with TEXT (need to cast it):
```SQL
-- using cast syntax
CAST(salary AS text);
-- shorter syntax
salary::text;
```
* `ILIKE` is the case insensitive version:
```SQL
name ILIKE 'MO%';
```

# Sort
___
```SQL
SELECT * FROM customers
ORDER BY <column> [ASC/DESC];

-- Order first name by ascending (default) and last name (descending);
SELECT first_name, last_name FROM employees
ORDER BY first_name, last_name DESC;

-- Order by with functions!
SELECT first_name, last_name FROM employees
ORDER BY LENGTH(first_name) DESC;
```
# Precedence
___
Operators in SQL follow the precedence:

![[operator precedence.png]]

# Dates
___
* ISO-8601 (*YYYY-MM-DDHH:MM:SS*);
* **Timestamp**: date with time and timezone info;
	* When creating a table and inserting data, you can declare if you want to account for timezone or not (will be appended after seconds);
* Usually, we use *Dates* and not *Timestamps*. Not a rule, though;
```SQL
-- Format date
SELECT TO_CHAR(CURRENT_DATE, 'dd/mm/yyyy');

-- Subtracting dates return the different in days
-- Age calculator
SELECT AGE(date '1800/01/01');

-- Only get day from a date
SELECT EXTRACT (DAY/MONTH/YEAR FROM date '1992/11/13') AS DAY;

-- Truncate
SELECT DATE_TRUNC('year', date '1992/11/13'); -- 1992-01-01

-- Intervals help write readable queries
INTERVAL '2 weeks ago';
INTERVAL '1 year 2 months 3 days';

SELECT *
FROM orders
WHERE purchased_date <= now() - INTERVAL '30 days';
```
# Multi Table Select
___
```SQL
SELECT a.emp_no, CONCAT(a.first_name, a.last_name) as "name",
       b.salary
FROM employees as a, salaries as b
WHERE a.emp_no = b.emp_no;
```
* Joins aggregate data from different tables based on a link (usually primary key with foreign key);

## Creating a Database
___
```SQL
-- Query 2

CREATE TABLE IF NOT EXISTS Actors (
Id INT AUTO_INCREMENT,
FirstName VARCHAR(20) NOT NULL,
SecondName VARCHAR(20) NOT NULL,
DoB DATE NOT NULL,
Gender ENUM('Male','Female','Other') NOT NULL,
MaritalStatus ENUM('Married', 'Divorced', 'Single', 'Unknown') DEFAULT "Unknown",
NetWorthInMillions DECIMAL NOT NULL,
PRIMARY KEY (Id));

```

