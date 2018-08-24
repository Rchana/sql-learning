Notes taken while completing the following online courses related to *SQL*:

https://campus.datacamp.com/courses/intro-to-sql-for-data-science
<br/>
https://www.udemy.com/introduction-to-databases-and-sql-querying/
<br/>
https://www.udacity.com/course/sql-for-data-analysis--ud198

Additional notes

# sql-learning

Just a reference page of random/relatively important/useful sql commands

### Creating temp tables
```
drop table if exists test_table;

create temporary table test_table as (
        select * from TABLE
        where agent is null
);
```

### Copying data INTO a new table
```
select * into test_table from TABLE
```

### Views are virtual tables. Think of a view as a definition on top of other tables that don't actually hold data. Data queried from a view dynamically changes as data in the underlying tables change. 
```
create view TABLE1 as (
select *
from TABLE2
where agent is null
);


create view TABLE1 as (
select *
from TABLE2
where agent is not null
);
```

### Seeing my changes to the test db 
```
select *
from TABLE
order by dt desc
;

select *
from TABLE
where case_num='20171121-167'
;
```

### UPDATE: used to update existing records in a table

```
UPDATE TABLE
SET agent = 'Arrchana';
```

### Creating a new table thats a copy of an existing table, with a new column
```
create table TABLE1 as (
select *, CAST(NULL AS char(6)) as assignee
from TABLE2
)
;
```

### Dropping a table if it exists
```
drop table if exists TABLE;
```
