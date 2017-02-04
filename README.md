# Introduction to SQL

## General Setup

If individuals don't want to use the mozilla firefox extension then we first need to check if the have SQLite installed.  
This can be done by typing in `which sqlite3` into the terminal.  If none comes up and then install instructions can be found
[here](https://www.tutorialspoint.com/sqlite/sqlite_installation.htm).

To load the data set use the command (EDIT - This causes problems downstream):  
`attach "UMichiganWorkStuff/Teaching_Courses/sql_dc_lesson/portal_mammals.sqlite" as db1;`

Better way to load data is with this commonad:  
`sqlite3 "UMichiganWorkStuff/Teaching_Courses/sql_dc_lesson/portal_mammals.sqlite"`

To quit use command  
`.quit`

## Basic Queries

**First Challenge Question**  
Write a query that returns The year, month, day, species_id and weight in mg

```sqlite3
SELECT year, month, day, species_id, ROUND(weight * 1000, 2)
FROM surveys;
```

**Second Challenge Question**  
Write a query that returns the day, month, year, species_id, and weight (in kg) for individuals 
caught on Plot 1 that weigh more than 75 g
  
```sqlite3
SELECT day, month, year, species_id, ROUND(weight / 1000, 4)
FROM surveys
WHERE (plot_id = 1) AND (weight > 75);
```

**Sorting**  
Write a query that returns year, species_id, and weight in kg from the surveys table, sorted with the largest weights at the top.

```sqlite3
SELECT year, species_id, ROUND(weight / 1000, 2)
FROM surveys
ORDER BY weight DESC;
```

**Order of Execution**  
Let’s try to combine what we’ve learned so far in a single query. Using the surveys table write a query to display the three date fields, species_id, and weight in kilograms (rounded to two decimal places), for individuals captured in 1999, ordered alphabetically by the species_id. Write the query as a single line, then put each clause on its own line, and see how more legible the query becomes!  

*All One Line*
```sqlite3
SELECT month, day, year, species_id, ROUND(weight / 1000, 2) FROM surveys WHERE (year = 1999) ORDER BY species_id ASC;
```
*Spaced Over Multiple Lines*  
```sqlite3
SELECT month, day, year, species_id, ROUND(weight / 1000, 2)
FROM surveys
WHERE (year = 1999)
ORDER BY species_id ASC;
```

## Aggregation

**COUNT and GROUP BY**  
Write a query that returns: total weight, average weight, and the min and max weights for all animals caught over the duration of the survey. Can you modify it so that it outputs these values only for weights between 5 and 10?  

*Base Answer*
```sqlite3
SELECT SUM(weight), AVG(weight), MIN(weight), MAX(weight)
FROM surveys;
```  

*Weights Between 5 and 10*
```sqlite3
SELECT SUM(weight), AVG(weight), MIN(weight), MAX(weight)
FROM surveys
WHERE (weight > 5) AND (weight < 10);
```

Write queries that return:
* How many individuals were counted in each year. a) in total; b) per each species.

a)
```sqlite3
SELECT species_id, COUNT(*)
FROM surveys
GROUP BY year;
```

b)
```sqlite3
SELECT species_id, COUNT(*)
FROM surveys
GROUP BY year, species_id;
```

* Average weight of each species in each year. Can you modify the above queries combining them into one?

```sqlite3
SELECT AVG(weight), species_id
FROM surveys
GROUP BY species_id, year;
```

**The "HAVING" keyword**

Write a query that returns, from the species table, the number of genus in each taxa, only for the taxa with more than 10 genus.

```sqlite3
SELECT genus, COUNT(genus) AS totals
FROM species
GROUP BY taxa
HAVING totals > 10;
```






