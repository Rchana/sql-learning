# sql-learning

The following notes were made while following *DataCamp*'s *Intro to SQL for Data Science* course:
https://campus.datacamp.com/courses/intro-to-sql-for-data-science/

*Note*: must use single quotes when using postgreSQL

## Selecting columns

#### `COUNT` allows you to count the number of entries that meet your condition

* If you want to select 'name' column from 'people' table:
`SELECT name FROM people;`

* If you want to select multiple columns from a table:
`SELECT name, birthdate FROM people;`

* Or select all columns:
`SELECT * FROM people;`

* If you want to limit the # of results requested: 
`SELECT * 
FROM people 
LIMIT 10;`

* If you want to select all the unique values from a column:
`SELECT DISTINCT language FROM films`

* If you want the number of rows in the 'people' table:
`SELECT COUNT(*) FROM people`

* If you want to count the number of non-missing values in a specific column:
`SELECT COUNT(birthdate) FROM people`

* If you want the distinct number of entries in a column:
`SELECT COUNT(DISTINCT birthdate) FROM people`

##  Filtering results

#### `WHERE` allows you to filter based on text/numeric.
`=` equal
`<>` not equal
`<` less than
`>` greater than
`<=` less than or equal to
`>=` greater than or equal to

* If you want to return a film with the title 'Metropolis':
`SELECT title FROM films WHERE title = 'Metropolis'`

* If you want to select all details from films with a budget over 10000:
`SELECT * FROM films WHERE budget > 10000`

* If you want to get the number of films released before 2000:
`SELECT COUNT(*) FROM films WHERE release_year < 2000`

* If you want the number of Hindi language films:
`SELECT COUNT(*) FROM films WHERE language='Hindi'`

* If you want the title of films released between 1994 & 2000:
`SELECT title FROM films WHERE release_year > 1994 AND release_year < 2000`

* If you want all the details for Spanish films released after 2000:
`SELECT * FROM films WHERE language = 'Spanish' and release_year > 2000`

* If you want titles form films released in either 1994 or 2000:
 `SELECT title FROM films WHERE release_year=1994 OR release_year=2000`

* If you want the title and release year of films released in the 90s which were in French or Spanish and which took in more than $2M gross:
`SELECT title, release_year
FROM films
WHERE (release_year >= 1990 AND release_year <2000)
AND (language = 'French' OR language = 'Spanish')
AND gross > 2000000`

* If you want titles of films released in and between 1994 and 2000:
`SELECT titles 
FROM films 
WHERE release_year
BETWEEN 1994 AND 2000`

#### `IN` allows you to specify multiple values in `WHERE`.

* If you want the title and release year of all films released in 1990 or 2000 that were longer than two hours (duration is in minutes):
`SELECT title, release_year 
FROM films
WHERE release_year IN (1990, 2000)
AND duration > 2*60`

* If you want the title of films that are in either English, Spanish or French:
`SELECT title, language 
FROM films
WHERE language IN ('English', 'Spanish', 'French')`

#### `NULL` represents a missing or unknown value. You can check for it using `IS NULL`.

* If you want to count the number of missing birth dates in the 'people' table:
`SELECT COUNT(*) FROM people WHERE birthdate IS NULL`

* If you want the names of people who are still alive:
`SELECT name FROM people WHERE deathdate IS NULL`

#### `LIKE` allows you to search for a pattern within a column.
#### `NOT LIKE` finds records that don't match the pattern specified.
#### `%` is a wildcard that will match 0, 1 or more characters.
#### `_` is a wildcard that will match a single character.

* If you want the names of companies that start with 'Data':
`SELECT name FROM companies WHERE name LIKE 'Data%'

* If you want the names of companies that follow the string pattern:
`SELECT name FROM companies WHERE name LIKE 'DataC_mp'

* If you want the names of people who have 'r' as the second letter:
`SELECT name FROM people WHERE name LIKE '_r%'`

* If you want the names of people whose names don't start with an 'A':
`SELECT name FROM people WHERE name NOT LIKE 'A%'`

## Aggregate functions
These allow you to perform calculations on data in a database

#### `AVG` returns the average value for the values in a column

* If you want the average budget of all films:
`SELECT AVG(budget) FROM films`

#### `MAX` returns the highest value in a column (likewise for `MIN`)

* If you want the max budget of all films:
`SELECT MAX(budget) FROM films`

#### `SUM` returns the result of adding up the numeric values in a column

* If you want the sum of all film budgets:
`SELECT SUM(budget) FROM films`

* If you want the total budget of movies made in 2010 or later:
`SELECT SUM(budget) FROM films WHERE release_year >= 2010`

* If you want the amount grossed by the worst performing film in 1994:
`SELECT MIN(gross) FROM films WHERE release_year = 1994`

#### Basic arithmetic operations: +, -, *, /

#### `AS` creates an alias

`SELECT (4/3) AS result` returns 1
`SELECT (4.0/3.0) AS result` returns 1.333333

*Note*: When dividing, make sure at least one of your numbers has a decimal place

* If you want title and net profit (gross-budget) for each film (alias net profit as net_profit):
`SELECT title, gross-budget AS net_profit FROM films`

* If you want the average duration in hours for all the films, using avg_duration_hours as an alias:
`SELECT AVG(duration/60.0) AS avg_duration_hours FROM films`

* If you want the % of people who are dead, using percentage_dead as an alias:
`SELECT COUNT(deathdate)*100/COUNT(*) AS percentage_dead FROM people`

* If you want the number of years between the newest and oldest film:
`SELECT MAX(release_year)-MIN(release_year) as difference FROM films`

## Sorting, grouping and joining results

#### `ORDER BY` sorts results in ascending or descending order according to values of 1 or more columns
#### `DESC` is used to sort in descending order because by defaul it sorts in ascending order

* If you want the titles of films sorted by release year, from newest to oldest:
`SELECT title FROM films ORDER BY release_year DESC`

* If you want the names in 'people' table in alpha order:
`SELECT name FROM people ORDER BY name`

* If you want titles of films released in 2000 or 2012, in order they were released:
`SELECT title FROM films WHERE release_year IN (2000,2012) ORDER BY release_year`

* If you want the title of movies that begin with 'M' and order them alphabetically:
`SELECT title FROM films WHERE title LIKE 'M%' ORDER BY title`

* If you want the IMDB score and film ID for every film from the reviews table, sorted from highest to lowest score:
`SELECT imdb_score, film_ID FROM reviews ORDER BY imdb_score DESC`

#### Sorting based on multiple columns

* If you want the release year, duration, and title of films ordered by their release year and duration:
`SELECT release_year, duration, title FROM films ORDER BY release_year, duration`

#### `GROUP BY` allows you to group a result by one or more columns

* If you want the release year and count of films released in each year:
`SELECT release_year, count(*) FROM films GROUP BY release_year`

* If you want the release year and largest budget for all films, grouped by release year:
`SELECT release_year, MAX(budget) FROM films GROUP BY release_year`

* If you want the release year and lowest gross earnings per release year:
`SELECT release_year, MIN(gross) FROM films GROUP BY release_year`

* If you want the language and total gross amount films in each language made:
`SELECT language, SUM(gross) FROM films GROUP BY language`

* if you want the release year, country, and highest budget spent making a film for each year, for each country. Sort your results by release year and country:
`SELECT release_year, country, MAX(budget) FROM films GROUP BY release_year, country`

#### `HAVING` enables you to filter based on results of aggregate functions because `WHERE` doesn't 

* If you want the years where more than 200 movies were released:
`SELECT release_year FROM films GROUP BY release_year HAVING COUNT(title)>200`

* If you want the average budget and gross earning for all films after 1990 grouped by year:
`SELECT release_year, AVG(budget) AS avg_budget, AVG(gross) AS avg_gross FROM films WHERE release_year > 1990 GROUP BY release_year`

* If you want the above query but only years with an average budget of greater than $60 million are included:
`SELECT release_year, 
AVG(budget) AS avg_budget, 
AVG(gross) AS avg_gross 
FROM films 
WHERE release_year > 1990
GROUP BY release_year
HAVING AVG(budget) > 60000000`

* If you want to moditfy the move such that you order the results from highest average gross earnings to lowest:
`SELECT release_year, 
AVG(budget) AS avg_budget, 
AVG(gross) AS avg_gross
FROM films
WHERE release_year > 1990
GROUP BY release_year
HAVING AVG(budget) > 60000000
ORDER BY avg_gross DESC;`

#### `LIMIT` limits the number of rows returned

* If you want to get the country, average budget, and average gross take of countries that have made more than 10 films. Order the result by country name, and limit the number of results displayed to 5. You should alias the averages as avg_budget and avg_gross respectively:
`SELECT country, 
AVG(budget) AS avg_budget, 
AVG(gross) AS avg_gross 
FROM films 
GROUP BY country 
HAVING COUNT(*) > 10 
ORDER BY country 
LIMIT 5`

#### `JOIN` allows you to use data from multiple tables

* If you want the IMDB score (from 'review' table) for the film 'To Kill a Mockingbird' (from 'films' table):
`SELECT title, imdb_score FROM films JOIN reviews ON films.id = reviews.film_id WHERE title = 'To Kill a Mockingbird'`
