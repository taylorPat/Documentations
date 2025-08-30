# Imperative vs. declarative

Declarative = What will happen?

No clue how it will happen. Example: SQL

Imperative = How it will happen?

Line by line instructions. Example: Java

# Database

= A structured set of data in a way to scale with organisations that have massive amount of data.

# Model

= A structure for the data. So it is a way of how to store and organize your data.

# DBMS

= Manages and organizes data inside your db following the model. You can use SQL to get / manipulate data and the DBMS will get / manipulate the data in a save and secure way.

- Makes sure that data is saved the right way (e.g. which data is saved on what hard drive)
- Sets security policies and permission so not everyone can touch your data
- Transactional management for safety guarantee

E.g.: MySQL, PostreSQL, Oracle
Each having different features (data management, security, ... )

# Relation model

Create relation between table.

Primary key is an unique identifier which can be used to create a link to other data.

In order to link data from different tables you define a foreign key which references a primary key of another table.

# SQL

## SQL commands

DCL (Data Control Language): Grant, Revoke
DDL (Data Definition Language): Create, Alter, Drop, Rename, Truncate, Comment
DQL (Data Query Language): Select
DML (Data Manipulation Language): Insert, Update, Delete, Merge, Call, Explain Plan, Lock Table

# Functions

Aggregate functions = Operate on many records to produce 1 value (e.g. SUM to get total amount of salaries, AVG, COUNT, MIN, MAX, SUM)

https://www.postgresql.org/docs/16/functions-aggregate.html

Scalar function = Operate on each record independently (e.g. CONCAT)

# Order of operations

FROM -> WHERE -> SELECT

# Operator precedence

A statement having multiple operators is evaluated based on the priority of operators.

Parantheses -> Multiplication / Division -> Subtraction / Addition -> Concatenation operators -> Comparison operators -> IS NULL, LIKE, NOT IN -> NOT -> AND -> OR

If operators have equal precedence then the operators are evaluated directionally from left to right or right to left.

https://www.postgresql.org/docs/12/sql-syntax-lexical.html#SQL-PRECEDENCE

# Null values

A record that does not have a value is considerd empty.
SELECT NULL = NULL -- NULL

No matter what you do with NULL it will always return NULL!!!

```sql
-- DON'T
SELECT * FROM test WHERE name != NULL;

-- DO
SELECT * FROM test WHERE name IS NOT NULL;
```

## COALESCE

Returns the first non NULL vaue in the list
`SELECT sum(COALESCE(age, 10)) FROM studens;`

## 3 value logic

SQL is different from other SQL statements in a way that it has three value logic system:
True, False, NULL (unknown)

Be always aware of NULL because it can lead to weird calculations.

# BETWEEN

```sql
SELECT * FROM table WHERE <column> BETWEEN X AND Y;

Same as:
SELECT * FROM table WHERE <column> > X AND <column> < Y;
```

# IN

```sql
SELECT * FROM table WHERE <column> in (<value1>, <value2>)
```

# Partial lookups / Pattern matching (LIKE)

Postgres only does the LIKE operation on TEXT. Therefore we have to CAST.
Pattern wildcards:

- '%' -> any number of characters
- '\_' -> one character
  Starts with M -> 'M%'

# ILIKE

Case insensitive pattern matching

# CAST

CAST(salary AS TEXT)
salary::TEXT

# Date time

```sql
SHOW TIMEZONE;

SET TIME ZONE 'UTC'; -- set time zone for session

SHOW TIMEZONE;

ALTER USER chang SET timezone='UTC'; -- set time zone globaly for user

SHOW TIMEZONE;

SELECT now();

CREATE TABLE timezones (
	ts TIMESTAMP WITHOUT TIME ZONE,
	tz TIMESTAMP WITH TIME ZONE
);

INSERT INTO timezones VALUES (
	TIMESTAMP WITHOUT TIME ZONE '2000-01-01 10:00:00-05', -- 2000-01-01 10:00:00
	TIMESTAMP WITH TIME ZONE '2000-01-01 10:00:00-05' -- 2000-01-01 15:00:00+00
)

SELECT * FROM timezones;
```

# DATE functions

```sql
SELECT NOW(); -- 2025-07-15 19:23:36.066427+00
SELECT NOW()::date; -- 2025-07-15
SELECT CURRENT_DATE; -- 2025-07-15
SELECT TO_CHAR(CURRENT_DATE, 'dd/mm_yyyy') -- 15/07_2025
SELECT TO_CHAR(CURRENT_DATE, 'WW') -- Calender week -> 28
SELECT NOW() - '1800/01/01' -- Intervals / Difference in Days: 82375 days 19:29:39.407188
SELECT DATE '1800/01/01' -- ISO 8601: 1800-01-01
SELECT AGE('1800/01/01'::DATE) -- 225 years 6 mons 14 days
SELECT AGE('1800/01/01'::DATE, '1996/01/01'::DATE) -- Interval: -196 years
SELECT TO_CHAR(AGE('1800/01/01'::DATE, '1996/01/01'::DATE), 'dd/mm/yyyy') -- 00/00/-0196
SELECT EXTRACT (DAY FROM DATE '1992/12/11'); -- 11
SELECT EXTRACT (MONTH FROM DATE '1992/12/11'); -- 12
SELECT EXTRACT (YEAR FROM DATE '1992/12/11'); -- 1992
-- Following command:
-- Sets everything smaller than (->month, day) year to
-- their smallest possible value
SELECT DATE_TRUNC('year', date '1992/11/13') -- 1992-01-01 00:00:00+00

-- Intervals
-- Get the employees which are more then 30 years in the company
-- "30 years before the current datetime"
SELECT * FROM employees
WHERE hire_date <= NOW() - INTERVAL '30 years';
-- Also usable with Months, Weeks, Days, Hours, Minutes
```

Excercises

```sql
-- Get employees above 60
SELECT AGE(birth_date),* FROM employees
WHERE EXTRACT( YEAR FROM AGE(birth_date)) > 60;

-- Get employees hired in February
SELECT * FROM employees
WHERE EXTRACT(MONTH FROM hire_date) = 2

-- Get employees born in January 1964
SELECT * FROM employees
WHERE DATE_TRUNC('month', birth_date) = DATE '1964-01-01'
```

# Remove duplicates

DISTINCT clause keeps one row for each group of duplicates

```sql
-- Get different titles of all employees
SELECT DISTINCT(title) FROM titles
```

## Join

A join combines columns from one table with those of another table based on a common column. (Most common approach: Link primary key to foreign key)

Joining data works as a filter and there are different ways of joining tables.

### Inner Join

Data is joined when both / multiple tables have the same row value for the joined column. Each row resulting in a match will be shown in the results.

Could be replaced with a SELECT plus WHERE condition but that is not efficient.

### Outer Join

#### Left (outer) join

Left joins add the data that do not have a match from the left table (table insid the FROM clause).

Example in the tutorial: Get all employees which are not managers.

Solution: Make a left join on employees and manager table and select all employees for which `m.emp_no is NULL`.

#### Right (outer) join

Is the opposite / inverse of Left join.

# Advanced SQL

## Group By

Summarizing or aggregating data by groups.

Splits data into groups so we can apply functions against those groups rather than the entire table.

Every column that is not part of the GROUP BY clause must apply a function.

_Split Apply Combine_
Split = Divide into Groups with values
Apply = Apply Aggregate against ungrouped columns
Combine = Combines Groups with a single value into single value

## Where vs. Having

Where applies filters to individual rows
Having applies filters to a group as a whole

Order of Operation:
FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY

```sql
/*
*   Show me all the employees, hired after 1991 and count the amount of positions they've had
*  Database: Employees
*/

SELECT first_name, last_name, count(emp_no) AS job_count FROM employees
WHERE EXTRACT (YEAR FROM hire_date) > 1991
GROUP BY first_name, last_name
ORDER BY job_count DESC
```

## Grouping Sets

Grouping is a subclass of GROUP BY that allows you to define multiple groupings.

```sql
/*
*  Calculate the total amount of employees per department and the total using grouping sets
*  Database: Employees
*  Table: Employees
*/
-- With UNION
SELECT
	GROUPING(de.dept_no), de.dept_no, COUNT(de.emp_no) AS employees_per_dep
FROM
	dept_emp AS de
GROUP BY
	de.dept_no

UNION -- use UNION ALL to keep duplicates

SELECT
	NULL, NULL, COUNT(de.emp_no) AS employees_per_dep
FROM
	dept_emp AS de
ORDER BY employees_per_dep DESC

-- With GROUPING SETS
SELECT
	GROUPING(de.dept_no), de.dept_no, COUNT(de.emp_no) AS employees_per_dep
FROM
	dept_emp AS de
GROUP BY
	GROUPING SETS (
		(),
		(de.dept_no)
	)
ORDER BY employees_per_dep DESC
```

## ROLL UP

Collects all the different combinations for your GROUPING SETS.

## Window function

Window functions create a new column based on functions performed on a subset or "window" of the data.

The window function applies against filters, but does not for LIMITS.

```sql
SELECT
	*,
	AVG(salary) OVER () as avg_global_salary
FROM salaries
```

### PARTITION BY

Divide rows into groups to apply the window function against [optional].

```sql
SELECT
	*,
	AVG(salary) OVER (PARTITION BY de.dept_no) as avg_global_salary
FROM salaries AS s
JOIN dept_emp AS de ON de.emp_no = s.emp_no
```

### ORDER BY acting strange

ORDER BY changes the frame of the window function.

ORDER BY tells the window function: "Take everything before me and myself into consideration".

It works like accumaltive sum in maths.

Framing changes the subset or window which is used for applying functions.

Without ORDER BY by default the framing is usually all partition rows.

With ORDER BY by default the framing is usually everything before the current row and the current row.

```sql
SELECT
	emp_no,
	COUNT(salary) OVER () as avg_global_salary
FROM salaries AS s
-- 10001 2844047
--   "      " (x17)
-- 10002 2844047
--   "      " (x6)
-- 10003 2844047
--   "      " (x7)

SELECT
	emp_no,
	COUNT(salary) OVER (PARTITION BY emp_no) as avg_global_salary
FROM salaries AS s
-- 10001 17
--   "      " (x17)
-- 10002 6
--   "      " (x6)
-- 10003 7
--   "      " (x7)

SELECT
	emp_no,
	COUNT(salary) OVER (ORDER BY emp_no) as avg_global_salary
FROM salaries AS s
-- 10001 17
--   "      " (x17)
-- 10002 6
--   "      " (x23)
-- 10003 7
--   "      " (x30)
```

### Framing / Window functions

Possible functions: https://www.postgresql.org/docs/16/functions-aggregate.html

```sql
/*
Get the current salary and first salary
Attention: Not the highest and lowest salary! <- Can be done easily with GROUP BY
*/
SELECT
	DISTINCT emp_no,
	LAST_VALUE(salary) OVER (
		PARTITION BY emp_no
		ORDER BY from_date
		RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
	) as latest_salary,
	FIRST_VALUE(salary) OVER (
		PARTITION BY emp_no
		ORDER BY from_date
		RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
	) as first_salary
FROM salaries AS s
```

ROW_NUMBER() gets no parameter and does not consider framing.

```sql
/*
*  Show the population per continent
*  Database: World
*  Table: Country
*/

SELECT continent, SUM(population) AS total_population
FROM country
GROUP BY continent
ORDER BY total_population

/*
*  To the previous query add on the ability to calculate the percentage of the world population
*  What that means is that you will divide the population of that continent by the total population and multiply by 100 to get a percentage.
*  Make sure you convert the population numbers to float using `population::float` otherwise you may see zero pop up
*  Try to use CONCAT AND ROUND to make the data look pretty
*
*  Database: World
*  Table: Country
*/
SELECT
	DISTINCT continent,
	SUM(population) OVER(PARTITION BY continent) AS total_continent_population,
	SUM(population) OVER() AS total_population,
	ROUND((SUM(population::FLOAT) OVER(PARTITION BY continent) / SUM(population::FLOAT) OVER()) * 100) AS relative_population
FROM country
ORDER BY relative_population DESC
```

## Conditional statement

Each return value must be a single value.

Can also be applied in a WHERE clause or in aggregate functions.

```sql
/**
* Database: Store
* Table: products
* Create a case statement that's named "price class" where if a product is over 20 dollars you show 'expensive'
* if it's between 10 and 20 you show 'average'
* and of is lower than or equal to 10 you show 'cheap'.
*/
SELECT
	*,
	CASE
		WHEN price > 20 THEN 'expensive'
		WHEN price BETWEEN 10 AND 20 THEN 'average'
		ELSE 'cheap'
	END
FROM products
```

## NULLIF

Return null if a condition is met.

```sql
/*
* DB: Store
* Table: products
* Question: Show NULL when the product is not on special (0)
*/

SELECT prod_id, title, price, NULLIF(special, 0) as "special"
FROM products
```

## Views

Views allow you to store and query previously run queries.
Views are the output of the query we ran.
Views act like tables.
Views at least non materialized need very little space.

_Materialized View_
Stores the data physically on the hard drive and periodically updates it when tables change

_NON-Materialized View_
Query gets re-run every time the view is called on
It is just a reference to the query. The result is not stored.

```sql
CREATE VIEW view_name AS <QUERY>

-- Update view
CREARE OR REPLACE VIEW view_name AS <QUERY>

-- Rename view
ALTER VIEW <view name> RENAME TO <view name>

-- Delete view
DROP VIEW [ IF EXISTS ] <view name>
```

```
/*
*  Create a view "90-95" that:
*  Shows me all the employees, hired between 1990 and 1995
*  Database: Employees
*/

CREATE OR REPLACE VIEW "90_95" AS
SELECT * FROM EMPLOYEES
WHERE EXTRACT(YEAR FROM hire_date) BETWEEN 1990 AND 1995;

/*
*  Create a view "bigbucks" that:
*  Shows me all employees that have ever had a salary over 80000
*  Database: Employees
*/
CREATE VIEW "bigbucks" AS
SELECT DISTINCT(emp_no) FROM salaries
WHERE salary > 80000
```

## Indexes

An index is a construct to improve query performance.

An index is a pointer to data in a table.

Speed up data query.

Slows down data insertion and updates.

### Types of indexes

- single column (retrieving data that satisfies ONE condition)
- multi column (retrieving data that satisfies MULTIPLE conditions)
- unique
- partial (index just a subset of a table e.g. salary > 80000)
- implicit indexes (automatically created by the database for primary key and unique key)

```sql
CREATE UNIQUE INDEX <name>
ON <table> (column1, column2, ...);

-- With a specific method
CREATE UNIQUE INDEX <name>
ON <table> (column1, column2, ...)
USING <method>;


DROP INDEX <name>
```

### When to use:

- Index foreign keys
- Index primary keys and unique columns
- Index on columns that end up in the ORDER BY / WHERE clause often

### When **not** to use:

- on small tables
- on frequently updated tables
- on columns that can contain null values
- on columns that have large values (e.g. comments, json)

### Index algorithms

- b-tree: default algorithm, best used for comparisons with <, <=, =, >=, BETWEEN, IN, IS (NOT) NULL
- hash: can only compare equality (=) operations
- gin: generalized inverted index, best used when multiple values are stored in a singled field (arrays)
- gist: generalized search tree, useful in indexing geometric data and full text search

## Subqueries

A construct that allows you to build extremly complex queries.

A query inside a query.

Most often found in the WHERE clause.

Also called inner query or inner select.

You can also use it in SELECT, FROM and HAVING clause.

```sql
SELECT * FROM <table>
WHERE <column> <condition> (
	SELECT <column>, <column>, ...
	FROM <table>
	[WHERE | GROUP BY | ORDER BY | ...]
)
```

Innser query must return a single record when used inside SELECT, WHERE, HAVING.

**Correlated Subquery**
You can reference from outside to inside means you can use the data of the outside query in the inside query. It is called correlated because without the parent (outer) query the inner query cannot exist.

### How does it differ from joins?

- Subqueries are queries that can stand alone
- Can return a single result or row set
- Subqueries results are immediatly used

- Joins just combine rows from one or more tables based on a match condition
- Can only return row set
- Joined table can be used in the outer query

```sql
/*
* DB: Employees
* Table: employees
* Question: Filter employees who have emp_no 110183 as a manager
*/
SELECT DISTINCT(E.emp_no) FROM EMPLOYEES AS E
JOIN DEPT_EMP AS DE ON DE.emp_no = E.emp_no
JOIN DEPT_MANAGER AS DM ON DM.dept_no = DE.dept_no
WHERE DM.emp_no = '110183'

SELECT * FROM EMPLOYEES
WHERE emp_no IN (
	SELECT emp_no FROM DEPT_EMP
	WHERE dept_no = (
		SELECT dept_no FROM DEPT_MANAGER WHERE emp_no = '110183'
	)
)

/*
* DB: Store
* Table: orders
* Question: Get all orders from customers who live in Ohio (OH), New York (NY) or Oregon (OR) state
* ordered by orderid
*/
SELECT * FROM orders AS o
INNER JOIN customers AS c ON c.customerid = o.customerid
WHERE c.state IN ('OH', 'NY', 'OR');

SELECT * FROM orders AS o
WHERE o.customerid IN (
	SELECT customerid FROM customers WHERE state IN('OH', 'NY', 'OR')
)
```

# DDL (Data Definition Language)

Commands to create, modify, delete different structures (e.g. database, table, roles).

## Types of Databases

Regular: Normal database that we are used to use.
Template: A blueprint which can be used for creating regular databases with predefined structure.

```sql
-- Create Database
CREATE DATABASE <db_name>
```

### Schemas

- To allow many user to use the same database without interferring with each other (bad case: different departments can have the same table names which would lead to interfering tables -> use schemas per department)
- To organize tables into logical groups to make them manageable
- Third party apps can be put into separate schemas so they do not collide with the names of other objects

## Roles in PostgreSQL

Roles determine what is allowed.

A role can be a user or a group of users.

Roles have the ability to grant membership to another role. (e.g. a role can give access to another role).

Roles are defined by attributes (e.g. SuperUser) and privileges (e.g. read access, write access).

Attributes:

- CREATEDB | NOCREATEDB
- CREATEROLE | NOCREATEROLE
- LOGIN | NOLOGIN
- SUPERUSER | NOSUPERUSER

```sql
-- psql -U postgres postgres -W
CREATE ROLE <ROLE_NAME> WITH LOGIN ENCRYPTED PASSWORD '<PASSWORD>';
-- or
CREATE USER <ROLE_NAME> WITH LOGIN ENCRYPTED PASSWORD '<PASSWORD>';
-- or through a postgresql binary
createuser --interactive -- Creates role without password therefore we add it manually
ALTER ROLE <ROLE_NAME> WITH LOGIN ENCRYPTED <PASSWORD>
```

```psql
# docker exec -it <CONTAINER_ID> /bin/sh
# Login
psql -U postgres postgres

# Get the roles
>>> \du
```

Only the creator of the database or the superuser has access to the database objects.

Show files which define the postgresql internals for login and interaction.
`>>>  show hba_file;` defines if you have to use password when you login via psql (e.g. trust)
`>>> show config_file;` define if encryption is on and what method is used for login

## Privileges

Attributes can only define so many privileges.

When you are not a superuser, you are fairly limited depending on what default privileges are set.

Privileges need to be granted for new roles and user in order to have access to certain data.

When managing roles and permissions always go with the principle of least privilege.

```sql
GRANT ALL PRIVILEGES ON <TABLE> TO <USER>
GRANT ALL ON ALL TABLES [IN SCHEMA <SCHEMA>] TO <USER>
GRANT [SELECT, UPDATE, INSERT, ...] ON <TABLE> [IN SCHEMA <SCHEMA>] TO <USER>
REVOKE [SELECT, UPDATE, INSERT, ...] ON <TABLE> FROM <USER>
```

```sql
-- psql -U postgres postgres -W
CREATE ROLE privilegetest WITH LOGIN ENCRYPTED PASSWORD 'password';
-- psql -U privilegetest postgres
\conninfo
-- You are connected to database "postgres" as user "privilegetest" via socket in "/var/run/postgresql" at port "5432".
SELECT * FROM titles;
-- ERROR:  permission denied for table titles
-- psql -U postgres postgres
GRANT SELECT ON titles TO privilegetest;
-- GRANT
REVOKE SELECT ON titles FROM privilegetest;
-- REVOKE
GRANT ALL ON ALL TABLES IN SCHEMA public TO privilegetest;
-- GRANT
REVOKE ALL ON ALL TABLES IN SCHEMA public FROM privilegetest;
-- REVOKE
CREATE ROLE employee_read;
-- CREATE ROLE
GRANT SELECT ON ALL TABLES IN SCHEMA public TO employee_read;
-- GRANT
-- \du
GRANT employee_read TO privilegetest;
-- GRANT ROLE
REVOKE employee_read FROM privilegetest;
-- REVOKE ROLE
```

## Data types

The data type is a constraint for a column to allow only specific type of data to be filled in.

It is important for the system to specify a type because it tells the system how it can / should handle the data in that column (e.g. selecting a proper index strategy).
If you store everything in text you could also use a excel sheet.

### Boolean

True (1, yes, y, t, true), False (0, no, f, flase), Null

### Character / text

PostgreSQL provides 3 types of character data:

- CHAR(N) -> Fixed length with space padding,
- VARCHAR(N) -> Variable length without space padding,
- TEXT() -> Unlimited length text

### Numeric

PostgreSQL has three INTEGERS (number that is not a fraction) types:

- SMALLINT -> -32768 to 32767
- INT -> -2147483648 to 2147483647
- BIGINT -> ... to ...

and a floating point (number that has a decimal point):

- FLOAT4 -> 6 Digits (it is precise up to the sixth character and the seventh is rounded)
- FLOAT8 -> 15 Digits (it is precise up to the 15th character and the 16th is rounded)
- DECIMAL / NUMERIC -> up to 131072 digits before the decimal point and up to 16383 digits after the decimal point

### Arrays

A group of elements of the same type.

```sql
CREATE TABLE test (
	column1 CHAR(2)[],
	column2 TEXT[],
	column3 FLOAT4[]
)

INSERT INTO test VALUES (
	ARRAY ["hi", "hu"],
	ARRAY ["No limit", "EIEIEIEIEIEI"],
	ARRAY [1.2, 3.5, 4.3, 5.5]
)
```

## Naming conventions

- Table names must be singular
- Columns must be lowercase with underscore
- Columns with mixed case are acceptable (e.g. studentID)
- Columns with uppercase are NOT acceptable

## Create table

```sql
-- Create extension for using uuid_generate_v4
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
-- CREATE EXTENSION
CREATE TABLE student (
student_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
first_name VARCHAR(255) NOT NULL,
last_name VARCHAR(255) NOT NULL,
email VARCHAR(255) NOT NULL,
date_of_birth DATE NOT NULL
);
-- CREATE TABLE
-- \dt
-- \d student
```

### Column constraints

- NOT NULL
- PRIMARY KEY
- UNIQUE (Can only contain unique values, NULL is unique)
- CHECK (Apply a special condition to check against the values in the columns)
- REFERENCES (Constrain the values of the column to only be values that exist in the column of another table (FOREIGN KEY))

### Table constraints

Is not tied to a particular column and it can encompass more than one column.

Every column constraint can be written as table constraint.

```sql
CREATE TABLE student (
student_id UUID DEFAULT uuid_generate_v4(),

CONSTRAINT pk_student_id PRIMARY KEY (student_id)
);
```

### UUID

Universally unique identifier.

You never have an conflict with identifier.

Generate unique identifiers for PK.

```sql
SELECT * FROM pg_available_extensions;
-- Create extension for using uuid_generate_v4
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
-- CREATE EXTENSION
```

Using UUIDs has pro and cons.

Pro:

- Unique everywhere
- It is easier to shard
- It is easier to merge / replicate than auto incremented numbers going from 1 to ...
- You expose less information about your system (ids are random)

Cons:

- Larger values to store
- Can have performance impact
- More difficult to debug

## Hands on: Creating a course

```sql
-- Custom data type
CREATE DOMAIN RATING SMALLINT CHECK (VALUE > 0 AND VALUE <= 5);

CREATE TYPE FEEDBACK AS (
	student_id UUID,
	rating RATING,
	feedback TEXT
);
```

```sql
-- Create all tables
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TABLE student (
student_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
first_name VARCHAR(255) NOT NULL,
last_name VARCHAR(255) NOT NULL,
email VARCHAR(255) NOT NULL,
date_of_birth DATE NOT NULL
);

CREATE TABLE SUBJECT (
	subject_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
	subject TEXT NOT NULL,
	description TEXT
);
CREATE TABLE TEACHER (
	teacher_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
	first_name VARCHAR(255) NOT NULL,
	last_name VARCHAR(255) NOT NULL,
	email VARCHAR(255) NOT NULL,
	date_of_birth DATE NOT NULL
);

CREATE TABLE COURSE (
	course_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
	"name" TEXT NOT NULL,
	description TEXT,
	subject_id UUID REFERENCES subject(subject_id),
	teacher_id UUID REFERENCES teacher(teacher_id),
	feedback Feedback[]
);

ALTER TABLE course ALTER column teacher_id SET NOT NULL;

CREATE TABLE ENROLLMENT (
	course_id UUID REFERENCES course(course_id),
	student_id UUID REFERENCES student(student_id),
	enrollment_date DATE NOT NULL,

	CONSTRAINT pk_enrollment PRIMARY KEY (course_id, student_id)
);
```

### Alter table

A very powerful command which has a lot of things it can change:
https://www.postgresql.org/docs/current/sql-altertable.html

- Change the SCHEMA of a table
- Change the name of a table-level CONSTRAINT
- Add and remove constraints
- Change a column level constraint

### Insert data

```sql
SELECT * FROM student;

INSERT INTO student (first_name, last_name, email, date_of_birth) VALUES (
	'Pat', 'Sch', 'pat@gmail.com', '1945-11-22'::DATE
);

INSERT INTO teacher (first_name, last_name, email, date_of_birth) VALUES (
	'Pat', 'Sch', 'pat@gmail.com', '1945-11-22'::DATE
);
SELECT * FROM teacher;

INSERT INTO subject (subject, description) VALUES ('SQL', 'A database management course');

SELECT * FROM course;

INSERT INTO course ("name", description, subject_id, teacher_id) VALUES (
	'c', 'c programming course', 'bb0de2b4-878e-4aa0-b529-c23566f9e0e2', '5fa6eef7-b0ec-4bad-8c89-ab1f2b710e82'
);

INSERT INTO enrollment
	(student_id, course_id, enrollment_date)
VALUES (
	'3db9f5d6-c50b-47f3-9728-1d5e857b9fd3', 'a36981e0-2fc5-4b08-ab96-6ba69f8e60f7',
	NOW()::DATE
)

UPDATE course SET feedback = array_append(
	feedback,
	-- Use ROW for a custom type like Feedback
	ROW(
		'3db9f5d6-c50b-47f3-9728-1d5e857b9fd3',
		5,
		'Gread course! Cheers'
	)::Feedback
)
WHERE course_id = 'a36981e0-2fc5-4b08-ab96-6ba69f8e60f7';
```

### Change custom feedback type to table

```sql
-- Rename existing feedback
ALTER TABLE course RENAME column feedback TO feedback_deprecated;
ALTER TYPE feedback RENAME TO feedback_deprecated;

CREATE TABLE feedback (
	student_id UUID NOT NULL REFERENCES student(student_id),
	course_id UUID NOT NULL REFERENCES course(course_id),
	rating RATING,
	feedback TEXT,

	CONSTRAINT pk_feedback PRIMARY KEY (student_id, course_id)
);
```

## Back up

### 1. What to backup?

Full backup = back ups all the data -> frequency: less often
Incremental backup = back ups since lase incremental -> frequency: often (daily, 2 days)
Differential backup = back ups since last full backup -> frequency: often (depends on how much data is changing in your system)
Transaction log = backup of the database transactions -> frequency: A lot (e.g. every 15th minute)

### 2. Whats the appropriate way to back up?

Are we back up at operating system, hardware level or just the database?

### 3. How frequently are we goint to do a backup?

Daily, weekly, bi-weekly? Dependent on how volatile the data is.

### 4. Decide where you are going to store data backups?

Cloud provider or own severs (on-premise)

### 5. have a retention policy for the backup

How long to I need to store this data? (e.g. medical for example 10 years)

## Back up in PostgreSQL

(https://www.postgresql.org/docs/12/backup.html)

PostgreSQL has the ability to back up and restore your data.

When we talk about that ability we talk about SQL Dump (= text file that gets generated from a database, can contain the schema, the tables or just the data).

SQL Dumps can be exported as scripts and can be run against a different RDBMS.
Or as back up files which can be read by PostgreSQL.

Tool: pgBackRest -> Backup and Restore tool (for differnt back up solution, not build in into PostgreSQL).

We created the SQL dump as SQL file in Valentina studio.

## Restoring in PostgreSQL

We restore the data by using psql

```sql
psql -d postgres

\conninfo
-- Connected to database postgres

-- Insert sql data
\i <FILE_NAME>
```

# Project

## Create database

```shell
# psql -U postgres
-- psql (16.9 (Debian 16.9-1.pgdg120+1))
-- Type "help" for help.

postgres=# CREATE DATABASE movr;
--CREATE DATABASE
postgres=# \c movr
-- You are now connected to database "movr" as user "postgres".
```

```shell
CREATE DATABASE movr_employees;
\c movr_employees
\i /project_data/9Ys9a7kFSfKK2rbPN1je_movr_employees.sql
```

## Insert schema and data

```shell
movr=# \i /project_data/MhohdFT4RIOmra3GWQ1k_schema.sql
CREATE TABLE
CREATE TABLE
CREATE INDEX
...
# \dt -> promo_codes, rides, ...
```

## Query data

```sql
--  It was an inside job
-- It was done on 2020-06-23
-- Keiko Corp Longitude: -74.997 to -74.9968
-- Keiko Corp Latitude: 40.5 to 40.6
-- A rider had contact to an employee


-- Create view for vehicle locations
CREATE VIEW vehicle_locations AS
SELECT * FROM vehicle_location_histories AS vlh
WHERE
	DATE_TRUNC('day', vlh.timestamp) = '2020-06-23'::DATE
	AND
	vlh.lat BETWEEN -74.997 AND -74.9968
	AND
	vlh.long BETWEEN 40.5 AND 40.6

-- Create view for all users on the ride and split first and last name
CREATE OR REPLACE VIEW user_splitted AS
SELECT DISTINCT
	split_part(u.name, ' ', 1) AS first_name,
	split_part(u.name, ' ', 2) AS last_name
FROM vehicle_locations AS vlh
JOIN rides AS r ON r.id = vlh.ride_id
JOIN users AS u ON u.id = r.rider_id;

create extension dblink; -- run this to run queries across databases
-- This will install a special utility called dblink that we can then use to query and filter across databases.

-- Create view for employees table inside movr_employees database
CREATE VIEW employees_external AS
SELECT *
FROM
	dblink('host=localhost user=postgres password=postgres dbname=movr_employees', 'SELECT emp_no, first_name, last_name FROM employees;')
	AS t1(emp_no NAME, first_name NAME, last_name NAME);

-- Get all possible thieves
SELECT DISTINCT
	CONCAT(us.first_name, ' ', us.last_name) AS rider_name,
	CONCAT(ee.first_name, ' ', ee.last_name) AS employee_name
FROM user_splitted AS us
JOIN employees_external AS ee ON ee.last_name = us.last_name
```
