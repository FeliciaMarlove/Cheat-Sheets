# SQL

- [Having](#having)
   * [Example](#example)
- [Window functions](#window-functions)
   * [Example](#example-1)
- [Predicates](#predicates)
- [Data types in operations](#data-types-in-operations)
- [Case](#case)
   * [Examples](#examples)
- [Joins](#joins)
- [Union](#union)
   * [Example](#example-2)
- [Intersect](#intersect)
   * [Example](#example-3)
- [Query evaluation order](#query-evaluation-order)

## Having

* To evaluate a group with a predicate (opposed to `where` = filters individual rows => doesn't work with sum, avg, count... ). 
* Only groups for which the predicate is true will be returned.
* Works with `group by`.
* In the expression `group by … having count(*) > 1`, * means "all in each group".

### Example

| orderid | custid | val |
|---------|--------|-----|
| 1       | 101    | 100 |
| 2       | 101    | 200 |
| 3       | 102    | 300 |
| 4       | 103    | 150 |
| 5       | 103    | 200 |
| 6       | 104    | 400 |

```
SELECT 
    orderid, custid, SUM(val) as total_sales
FROM Sales
GROUP BY orderid, custid
HAVING SUM(val) > 250
ORDER BY total_sales DESC
```

| orderid | custid | total_sales |
|---------|--------|-------------|
| 6       | 104    | 400         |
| 3       | 102    | 300         |

## Window functions

* `over` = window of rows (set of rows) on which the operation is applied.
* `Row_number()` assigns a unique sequential integer to the rows in the partition. Ordering is important, otherwise it might not be deterministic. If ordering on a non-unique column it's not deterministic either => add an attribute to the `order by` to make a unique combination. 
* The `order by` in the window function doesn't change the presentation of the final result.

### Example

Order by val in each partition, then assign a row number to each row by partition.

| orderid | custid | val |
|---------|--------|-----|
| 1       | 101    | 300 |
| 2       | 101    | 200 |
| 3       | 102    | 400 |
| 4       | 101    | 100 |
| 5       | 102    | 300 |

```
SELECT orderid, custid, val,
ROW_NUMBER() OVER(
    PARTITION BY custid
    ORDER BY val) AS rownum
FROM Sales
ORDER BY custid, val; 
```

| orderid | custid | val | rownum |
|---------|--------|-----|--------|
| 4       | 101    | 100 | 1      |
| 2       | 101    | 200 | 2      |
| 1       | 101    | 300 | 3      |
| 5       | 102    | 300 | 1      |
| 3       | 102    | 400 | 2      |

## Predicates

* Possible values: true, false and unknown (handled as a false value).
* A predicate where the value is null (except in expressions `is null` and `is not null`) will return unknown.
* Examples of predicates: `in`, `between`, `like`, comparison operators.

## Data types in operations

* The result of a division of 2 integers is an integer. Example: 3 / 2 = 1 .
* In scalar expressions with two operands, the first type takes precedence.
* Explicit casting can be used to keep whole values.

## Case

* Can be used as a scalar expression wherever it's allowed (`select`, clauses in `where`/`having`/`order by`, `check`constraint).
* If there is no match and no default (else) clause, it will return null.

### Examples

```
SELECT productid, productname, categoryid, 
    CASE categoryid 
        WHEN 1 THEN 'Beverages' 
        WHEN 2 THEN 'Solid food' 
        ELSE 'Unknown Category' 
    END  
AS categoryname 
FROM Products; 
```
 
```
...
    CASE  
        WHEN price < 7 THEN '5%'  
        WHEN price <= 10 THEN '10%'  
        WHEN price > 10 THEN '15%'
... 
```

## Joins

* Inner join = select records that have matching values in both tables.
* Outer joins
  * Left join = select all records from table 1 and matching records in table 2 (or null for records where there is no match in table 2).
  * Right join = contrary to left join.
  * Full join = select all records from joined tables (match in left or right).
  * Rows of the preserved table (left/right/both) are preserved.
  * Rows from the preserved table that did not find matches in the other table based on the predicate (`on`) will have null values on the attributes of the non-preserved table. 
  * The where clause should probably filter on an attribute of the preserved table (except cases where we specifically filter on null/not null). 
  * One common cause of bug when joining several tables is to have an inner join, after some outer joins, that joins on an attribute of the non-preserved table. Generally speaking, outer rows are dropped whenever any kind of outer join (left, right, or full) is followed by a subsequent inner join or right outer join.
  * `count(*)` counts all rows including null values in any column, useful to know the total number of rows returned.
  * `count(column)` counts rows where the specified column in not null, useful to know the number of rows where there was a matched value for that column (on the non-preserved side). 

## Union

* `union all` returns all rows that appear in any of the sets.
* `union` (implicit distinct) returns all unique rows from sets.

### Example

```
SELECT productid, productname, categoryid FROM products_2023 
UNION {ALL} 
SELECT productid, productname, categoryid FROM products_2024;
```

## Intersect

* Returns the results common to two sets.
* Implicitly distinct.

### Example

```
SELECT productid, productname, categoryid FROM products_2023 
INTERSECT {ALL} 
SELECT productid, productname, categoryid FROM products_2024;
```

## Query evaluation order

The SQL query evaluation order is roughly: 

1. FROM 
2. JOIN (if present) 
3. WHERE 
4. GROUP BY 
5. Aggregations  
6. HAVING
7. SELECT 
8. ORDER BY 

Therefor, if we define an alias in the `select` clause we cannot reference it in the `having` clause because it's evaluated before! However, we can use it in the `order by`. 