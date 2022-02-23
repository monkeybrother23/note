# PostgreSql 13.4

## 模式

```sql
-- 创建一个模式
CREATE SCHEMA myschema;
-- 删除一个为空的模式（其中的所有对象已经被删除）
DROP SCHEMA myschema;
-- 删除一个模式以及其中包含的所有对象：
DROP SCHEMA myschema CASCADE;
-- 查询所有模式
select * from information_schema.schemata;

```

## 表

### CREATE

```sql
CREATE TABLE myschema.company(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT    NOT NULL,
   AGE            INT     NOT NULL,
   ADDRESS        CHAR(50),
   SALARY         REAL
);
-- 查看模式下的所有表
postgres=# \dt myschema.*
                关联列表
 架构模式 |  名称   |  类型  |  拥有者
----------+---------+--------+----------
 myschema | company | 数据表 | postgres
-- 查看表结构
postgres=# \d myschema.company
              数据表 "myschema.company"
  栏位   |     类型      | 校对规则 |  可空的  | 预设
---------+---------------+----------+----------+------
 id      | integer       |          | not null |
 name    | text          |          | not null |
 age     | integer       |          | not null |
 address | character(50) |          |          |
 salary  | real          |          |          |
索引：
    "company_pkey" PRIMARY KEY, btree (id)
-- 修改表名 
ALTER TABLE old_name RENAME TO new_name;    
```

### DROP

```sql
DROP TABLE SCHEMA.table_name;
-- drop table myschema.company
```

### COLUMN

```sql
-- null
ALTER TABLE "develop"."company"  ALTER COLUMN "address" SET NOT NULL;
ALTER TABLE "develop"."company"  ALTER COLUMN "address" DROP NOT NULL;
-- change 数据长度
ALTER TABLE "develop"."company" 
  ALTER COLUMN "address" TYPE char(40) COLLATE "pg_catalog"."default";
-- change 数据类型
ALTER TABLE "develop"."company" 
  ALTER COLUMN "age" TYPE char(20) USING "age"::char(20);
ALTER TABLE "develop"."company" 
  ALTER COLUMN "age" TYPE int4 USING "age"::int4;
-- DROP
ALTER TABLE "develop"."company" 
  DROP COLUMN "salary";
```

## INSERT INTO

```sql
INSERT INTO products VALUES (1, 'Cheese', 9.99);

INSERT INTO products (product_no, name, price) VALUES (1, 'Cheese', 9.99);

INSERT INTO products (name, price, product_no) VALUES ('Cheese', 9.99, 1);


INSERT INTO products (product_no, name, price) VALUES
    (1, 'Cheese', 9.99),
    (2, 'Bread', 1.99),
    (3, 'Milk', 2.99);
    
INSERT INTO products (product_no, name, price)
SELECT product_no, name, price FROM new_products WHERE release_date = 'today';
```
## UPDATE
```sql
UPDATE products SET price = 10 WHERE price = 5;

UPDATE mytable SET a = 5, b = 3, c = 1 WHERE a > 0;
```

## DELETE
```sql
DELETE FROM products WHERE price = 10;
```
## RETURNING
```sql
CREATE TABLE users (firstname text, lastname text, id serial primary key);

INSERT INTO users (firstname, lastname) VALUES ('Joe', 'Cool') RETURNING id;


UPDATE products SET price = price * 1.10
WHERE price <= 99.99
RETURNING name, price AS new_price;

DELETE FROM products
WHERE obsoletion_date = 'today'
RETURNING *;
```
## SELECT

## 连接表
```sql
T1 CROSS JOIN T2 
即笛卡尔积等效于FROM T1,T2
INNER JOIN
对于 T1 的每一行 R1，生成的连接表都有一行对应 T2 中的每一个满足和 R1 的连接条件的行。

LEFT OUTER JOIN
首先，执行一次内连接。然后，为 T1 中每一个无法在连接条件上匹配 T2 里任何一行的行返回一个连接行，该连接行中 T2 的列用空值补齐。因此，生成的连接表里为来自 T1 的每一行都至少包含一行。

RIGHT OUTER JOIN
首先，执行一次内连接。然后，为 T2 中每一个无法在连接条件上匹配 T1 里任何一行的行返回一个连接行，该连接行中 T1 的列用空值补齐。因此，生成的连接表里为来自 T2 的每一行都至少包含一行。

FULL OUTER JOIN
首先，执行一次内连接。然后，为 T1 中每一个无法在连接条件上匹配 T2 里任何一行的行返回一个连接行，该连接行中 T2 的列用空值补齐。同样，为 T2 中每一个无法在连接条件上匹配 T1 里任何一行的行返回一个连接行，该连接行中 T1 的列用空值补齐
```

## GROUPING SETS
```sql
=> SELECT * FROM items_sold;
 brand | size | sales
-------+------+-------
 Foo   | L    |  10
 Foo   | M    |  20
 Bar   | M    |  15
 Bar   | L    |  5
(4 rows)

=> SELECT brand, size, sum(sales) FROM items_sold GROUP BY GROUPING SETS ((brand), (size), ());
 brand | size | sum
-------+------+-----
 Foo   |      |  30
 Bar   |      |  20
       | L    |  15
       | M    |  35
       |      |  50
(5 rows)
```
## ROLLUP
```sql
ROLLUP ( e1, e2, e3, ... )
表示给定的表达式列表及其所有前缀（包括空列表），因此它等效于
GROUPING SETS (
    ( e1, e2, e3, ... ),
    ...
    ( e1, e2 ),
    ( e1 ),
    ( )
)


ROLLUP ( a, (b, c), d )

GROUPING SETS (
    ( a, b, c, d ),
    ( a, b, c    ),
    ( a          ),
    (            )
)
```

## CUBE 
```sql
CUBE ( a, b, c )
表示给定的列表及其可能的子集（即幂集）。因此
GROUPING SETS (
    ( a, b, c ),
    ( a, b    ),
    ( a,    c ),
    ( a       ),
    (    b, c ),
    (    b    ),
    (       c ),
    (         )
)


CUBE ( (a, b), (c, d) )


GROUPING SETS (
    ( a, b, c, d ),
    ( a, b       ),
    (       c, d ),
    (            )
)
```



## 组合查询

```sql
query1 UNION [ALL] query2
query1 INTERSECT [ALL] query2
query1 EXCEPT [ALL] query2
```
## 行排序

```sql
order
```
## LIMIT和OFFSET
```sql
SELECT select_list
    FROM table_expression
    [ ORDER BY ... ]
    [ LIMIT { number | ALL } ] [ OFFSET number ]
```

## VALUES列表
```sql
=> SELECT * FROM (VALUES (1, 'one'), (2, 'two'), (3, 'three')) AS t (num,letter);
 num | letter
-----+--------
   1 | one
   2 | two
   3 | three
(3 rows)
```


## 递归查询
```sql
CREATE TABLE employees (
   employee_id serial PRIMARY KEY,
   full_name VARCHAR NOT NULL,
   manager_id INT
);

INSERT INTO employees (
   employee_id,
   full_name,
   manager_id
)
VALUES
   (1, 'Michael North', NULL),
   (2, 'Megan Berry', 1),
   (3, 'Sarah Berry', 1),
   (4, 'Zoe Black', 1),
   (5, 'Tim James', 1),
   (6, 'Bella Tucker', 2),
   (7, 'Ryan Metcalfe', 2),
   (8, 'Max Mills', 2),
   (9, 'Benjamin Glover', 2),
   (10, 'Carolyn Henderson', 3),
   (11, 'Nicola Kelly', 3),
   (12, 'Alexandra Climo', 3),
   (13, 'Dominic King', 3),
   (14, 'Leonard Gray', 4),
   (15, 'Eric Rampling', 4),
   (16, 'Piers Paige', 7),
   (17, 'Ryan Henderson', 7),
   (18, 'Frank Tucker', 8),
   (19, 'Nathan Ferguson', 8),
   (20, 'Kevin Rampling', 8);
   

WITH RECURSIVE subordinates AS (
   SELECT
      employee_id,
      manager_id,
      full_name
   FROM
      employees
   WHERE
      employee_id = 2
   UNION
      SELECT
         e.employee_id,
         e.manager_id,
         e.full_name
      FROM
         employees e
      INNER JOIN subordinates s ON s.employee_id = e.manager_id
) SELECT
   *
FROM
   subordinates;
   
   
employee_id | manager_id |    full_name
-------------+------------+-----------------
           2 |          1 | Megan Berry
           6 |          2 | Bella Tucker
           7 |          2 | Ryan Metcalfe
           8 |          2 | Max Mills
           9 |          2 | Benjamin Glover
          16 |          7 | Piers Paige
          17 |          7 | Ryan Henderson
          18 |          8 | Frank Tucker
          19 |          8 | Nathan Ferguson
          20 |          8 | Kevin Rampling
(10 rows)
```




## NULL
```sql
select case when coalesce(name,'') = '' then '姓名为空' else name end from student
```sql
## 模糊查询

```sql
--查询 hello 开头的数据
SELECT *
FROM demo
WHERE name LIKE 'hello%';

--查询以一个任意字符开始然后 end 开头的数据
SELECT *
FROM demo
WHERE name LIKE '_end';
```

## 分组查询

```sql
--分组查询取第一个


select name,
      score,
      course,
      rank() over(partition by course order by score desc) as rank
  from jinbo.student;
--rank over () 可以把成绩相同的两名是并列，如下course = 2 的结果rank值为：1 2 2 4 5
 name  | score | course | rank 
-------+-------+--------+------
 dock  |   100 |      1 |    1
 bob   |    90 |      1 |    2
 cark  |    80 |      1 |    3
 elic  |    70 |      1 |    4
 hill  |    60 |      1 |    5
 alice |    60 |      1 |    5
 test  |       |      2 |    1
 iris  |    80 |      2 |    2
 jacky |    80 |      2 |    2
 frank |    70 |      2 |    4
 grace |    50 |      2 |    5
--dense_rank()和rank over()很相似，可以把学生成绩并列不间断顺序排名，如下course = 2 的结果rank值为：1 2 2 3 4
select name,score,
      course,
      dense_rank() over(partition by course order by score desc) as rank
  from jinbo.student;
 name  | score | course | rank 
-------+-------+--------+------
 dock  |   100 |      1 |    1
 bob   |    90 |      1 |    2
 cark  |    80 |      1 |    3
 elic  |    70 |      1 |    4
 hill  |    60 |      1 |    5
 alice |    60 |      1 |    5
 test  |       |      2 |    1
 iris  |    80 |      2 |    2
 jacky |    80 |      2 |    2
 frank |    70 |      2 |    3
 grace |    50 |      2 |    4

select name,score,
      course,
      row_number() over(partition by course order by score desc) as rank
  from jinbo.student;
 name  | score | course | rank 
-------+-------+--------+------
 dock  |   100 |      1 |    1
 bob   |    90 |      1 |    2
 cark  |    80 |      1 |    3
 elic  |    70 |      1 |    4
 hill  |    60 |      1 |    5
 alice |    60 |      1 |    6
 test  |       |      2 |    1
 iris  |    80 |      2 |    2
 jacky |    80 |      2 |    3
 frank |    70 |      2 |    4
 grace |    50 |      2 |    5


--rank over(partition by course order by score desc nulls last)
```
UPDATE
    table_name a
SET
    a_name = r.map_value
FROM
    (
    SELECT
        map_key,
        map_value
    FROM
        table_name a) r
WHERE
    a.a_key = r.map_key
