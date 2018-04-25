# Udemy: Intro to Databases and SQL Querying

These following notes were made while following the following Udemy course:
https://www.udemy.com/introduction-to-databases-and-sql-querying/

## Basics

**Database**: organized collection of data; collection of tables, queries, reports, views and other objects
<br/>
**Tool (or IDE)**: something that feeds your queries to the database and returns data in a readable format 
<br/>
**Structured data**: data that fits into tables
<br/>
<br/>
**Getting started**:
1. Understand the data that needs to be stored
2. Create tables accordingly
3. Store data 
4. Query it

## Environment setup

Download **Microsoft SQL server Express**: 
https://www.microsoft.com/en-ca/sql-server/sql-server-downloads

## Intro to Querying

```create database mytestdb```

This creates a table within the mytestdb database: 
```
use mytestdb
create table mytesttable
(
rollno int,
firstname varchar(50),
lastname varchar(50),
)
```

This retrieves data from a database:
```
use mytestdb
select rollno, firstname, lastname from mytesttable
```

This allows you to insert data into your table:
```
insert into mytesttable(rollno, firstname, lastname)
values(1, 'ARRCHANA', 'PRADEEPAN')
```

**Download sample data:**
https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks2012

Steps to import a downloaded database into the IDE:
* Right click on Databases in the left column
* Click on Attach...
* Input your MDF file location
* Database should be loaded, and you can scroll through all the tables

```
use [AventureWorks2012]

SELECT * FROM [HumanResources].[Department]

-- SHOW ME ALL THE DEPARTMENT NAMES.
SELECT NAME FROM [HumanResources].[Department]

-- SHOW ME ALL THE GROUPS.
SELECT GROUPNAME FROM [HumanResources].[Department]

-- SHOW ME ALL THE **DISTINCT** GROUPNAME
SELECT DISTINCT GROUPNAME FROM [HumanResources].[Department]

-- SHOW ME ALL THE DEPARTMENT NAME WHO ARE PART OF MANUFACTURING (filtering using text)
SELECT NAME, GROUPNAME FROM [HumanResources].[Department]
WHERE GROUPNAME LIKE 'MANUFACTURING'

-- GIVE ME ALL THE EMPLOYEES FROM THE EMPLOYEE TABLE
SELECT * FROM [HumanResources].[Employee]

-- GIVE ME A LIST OF ALL EMPLOYEES WHO HAVE AN ORGLEVEL = 2
SELECT * FROM [HumanResources].[Employee] WHERE ORGANIZATIONLEVEL = 2

-- GIVE ME A LIST OF ALL EMPLOYEES WHO HAVE ORGLEVEL = 2 or 3
SELECT * FROM [HumanResources].[Employee] WHERE ORGANIZATIONLEVEL IN (2,3)

-- GIVE ME A LIST OF EMPLOYEES WHO HAVE A TITLE AS FACILITIES MANAGER (not case sensititve for text, exact match)
SELECT * FROM [HumanResources].[Employee] WHERE JOBTITLE LIKE 'FACILITIES MANAGER'

-- GIVE ME A LIST OF EMPLOYEES WHO HAVE THE WORD MANAGER IN THEIR TITLE (pattern matching, wildcard)
SELECT * FROM [HumanResources].[Employee] WHERE JOBTITLE LIKE '%Manager%'
```

#### Filtering Dates

```
-- GIVE ME ALL EMPLOYEES WHO ARE BORN AFTER Jan 1, 1980
SELECT * FROM [HumanResources].[Employee] WHERE Birthdate > '1/1/1980'

-- GIVE ME ALL EMPLOYEES WHO ARE BORN BETWEEN Jan 1, 1970 & Jan 1, 1980
SELECT * FROM [HumanResources].[Employee] WHERE Birthdate > '1/1/1970' AND Birthday < '1/1/1980'
-- Or... 
SELECT * FROM [HumanResources].[Employee] WHERE Birthdate BETWEEN '1/1/1970' AND '1/1/1980'
```

#### Creating Columns & Tables
**Alias**: any name you can give to a column; uses keyboard *AS*

```
SELECT NAME, LISTPRICE FROM [Production].[Product]
-- CALCULATED COLUMN:
SELECT NAME, LISTPRICE, LISTPRICE + 10 AS ADJUSTED_LIST_PRICE FROM [Production].[Product]
-- INTO (FOR STORING) - CREATES A NEW PERMANENT TABLE (PRODUCTION.PRODUCT_2) IS CREATED:
SELECT NAME, LISTPRICE, LISTPRICE + 10 AS ADJUSTED_LIST_PRICE INTO [Production].[Product_2] FROM [Production].[Product]

SELECT * FROM [Production].[Product_2]

-- CREATING A TEMPORARY TABLE (only valid within this session):
SELECT NAME, LISTPRICE, LISTPRICE + 10 AS ADJUSTED_LIST_PRICE INTO #tmpname FROM [Production].[Product]

SELECT * FROM #tmpname
```

#### Removing Data
```
-- DELETE DATA FROM TABLE
DELETE FROM [Production].[Product_2]
WHERE NAME LIKE 'Bearing Ball'

```

#### Update Statement
```
UPDATE [Production].[Product_2]
SET NAME = 'BLADE_NEW'
WHERE NAME LIKE 'BLADE'

SELECT * FROM [Production].[Product_2]
```

## Joins
Enable you to join 2+ tables, where at least 1 column is common between tables
* Inner join: only get rows that are common across both tables
* Outer join: introduce a 3rd table instead of common rows
    * **Left outer join** (take everything from the left table) vs. **Right outer join** (take everything from the right table)
    * If something doesn't match, replace with null 
    * **Full outer join** (take everything from left/right table)
* Cross join: every row from table 1 is joined with every row from table 2, and vise versa

**Interactive example**:

**Creating the data:** 
```
CREATE TABLE MYEMPLOYEE (EMPLOYEEID INT, FIRSTNAME VARCHAR(20), LASTNAME VARCHAR(20))

INSERT INTO MYEMPLOYEE VALUES(1, 'Michael', 'Scott')
INSERT INTO MYEMPLOYEE VALUES(2, 'Pam', 'Beesly')
INSERT INTO MYEMPLOYEE VALUES(3, 'Dwight', 'Schrute')

SELECT * FROM MYEMPLOYEE

CREATE TABLE MYSALARY (EMPLOYEEID INT, SALARY FLOAT)

INSERT INTO MYSALARY VALUES(1, 10000)
INSERT INTO MYSALARY VALUES(2, 8000)
INSERT INTO MYSALARY VALUES(3, 6000)

SELECT * FROM MYEMPLOYEE
SELECT * FROM MYSALARY 
```

**Inner joins:** gives you the rows that are common between the 2 tables
```
SELECT A.FIRSTNAME, A.LASTNAME, B.SALARY
FROM MYEMPLOYEE A INNER JOIN MYSALARY B ON A.EMPLOYEEID = B.EMPLOYEEID
```

**Left outer joins:** takes every row from left table and common rows from right table (if unmatched, fill with null)
```
CREATE TABLE MYPHONE(EMPLOYEEID INT, PHONENUMBER INT)
INSERT INTO MYPHONE VALUES(1, 12121212)
INSERT INTO MYPHONE VALUES(2, 36366363)

SELECT * FROM MYPHONE

SELECT A.FIRSTNAME, A.LASTNAME, B.PHONENUMBER FROM MYEMPLOYEE A LEFT JOIN MYPHONE B
ON A.EMPLOYEEID = B.EMPLOYEEID
```

**Right outer join:** takes every row from right table and common rows from left table (if unmatched, fill with null)
```
CREATE TABLE MYPARKING(EMPLOYEEID INT, PARKINGSPOT VARCHAR(20))

INSERT INTO MYPARKING VALUES (1, 'a1')
INSERT INTO MYPARKING VALUES (2, 'a2')

SELECT * FROM MYEMPLOYEE
SELECT * FROM MYPARKING

SELECT A.PARKINGSPOT, B.FIRSTNAME, B.LASTNAME FROM MYPARKING A RIGHT JOIN MYEMPLOYEE B ON A.EMPLOYEEID = B.EMPLOYEEID
```
**Full outer join**: take data from both tables (unmatch data gets filled with null)
```
CREATE TABLE MYCUSTOMER(CUSTOMERID INT, CUSTOMERNAME VARCHAR(20))

TRUNCATE TABLE MYCUSTOMER
INSERT INTO MYCUSTOMER VALUES (1, 'JOHN')
INSERT INTO MYCUSTOMER VALUES (3, 'BOB')

CREATE TABLE MYORDER(ORDERNUMBER INT, ORDERNAME VARCHAR(20), CUSTOMERID INT)

INSERT INTO MYORDER VALUES (1, 'SOMEORDER1', 1)
INSERT INTO MYORDER VALUES (2, 'SOMEORDER2', 2)
INSERT INTO MYORDER VALUES (3, 'SOMEORDER3', 7)
INSERT INTO MYORDER VALUES (4, 'SOMEORDER4', 8)

SELECT * FROM MYCUSTOMER
SELECT * FROM MYORDER

SELECT A.CUSTOMERID, A.CUSTOMERNAME, B.ORDERNUMBER, B.ORDERNAME
FROM MYCUSTOMER A FULL OUTER JOIN MYORDER B
ON A.CUSTOMERID = B.CUSTOMERID
```
**Cross Join**: every row from both tables gets matched up (maximum number of combinations)
```
SELECT * FROM MYCUSTOMER CROSS JOIN MYSALARY
```
Or alternatively could write:
```
SELECT * FROM MYCUSTOMER, MYSALARY
```
#### Date manipulation
```
SELECT GETDATE()
SELECT GETDATE()-2
```
```
SELECT DATEPART(yyyy, GETDATE()) AS YEARNUMBER
SELECT DATEPART(mm, GETDATE())
SELECT DATEPART(dd, GETDATE())
```
```
SELECT DATEADD(day, 4, GETDATE())
SELECT DATEADD(day, 4, '7/4/2015')
SELECT DATEADD(month, 4, GETDATE())
SELECT DATEADD(YEAR, 4, GETDATE())
```
```
SELECT TOP 10 * FROM [Production].[WorkOrder]
```
```
SELECT workOrderID, ProductID, StartDate, EndDate, DATEDIFF(day, StartDate, EndDate)
FROM [Production].[WorkOrder]
```
Subtract prev date from todays date:
```
SELECT DATEADD(dd, -(DATEPART(day, GETDATE())-1), GETDATE())
```
#### Aggregate functions
