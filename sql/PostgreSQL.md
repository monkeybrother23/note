# PostgreSql 13.4

## 模式

```sql
-- 创建一个模式
create schema develop;
-- 删除一个为空的模式（其中的所有对象已经被删除）
drop schema develop;
-- 删除一个模式以及其中包含的所有对象：
drop schema develop cascade;
-- 查询所有模式
select	* from	information_schema.schemata;
```

## 表

### CREATE

```sql
-- DROP TABLE develop.company;

CREATE TABLE develop.company (
	id int4 NOT NULL,
	company_name varchar(20) NOT NULL,
	age int4 NULL,
	address varchar(20) NOT NULL,
	CONSTRAINT company_pkey PRIMARY KEY (id)
);
-- 查看模式下的所有表
postgres=# \dt develop.*
                关联列表
 架构模式 |  名称   |  类型  |  拥有者
----------+---------+--------+----------
 develop  | company | 数据表 | postgres
(1 行记录)
-- 查看表结构
postgres=# \d develop.company
                     数据表 "develop.company"
     栏位     |         类型          | 校对规则 |  可空的  | 预设
--------------+-----------------------+----------+----------+------
 id           | integer               |          | not null |
 company_name | character varying(20) |          | not null |
 age          | integer               |          |          |
 address      | character varying(20) |          | not null |
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
### INSERT

```sql
INSERT INTO products VALUES (1, 'Cheese', 9.99);

INSERT INTO products (product_no, name, price) VALUES (1, 'Cheese', 9.99);
-- 批量操作
INSERT INTO products (product_no, name, price) VALUES
    (1, 'Cheese', 9.99),
    (2, 'Bread', 1.99),
    (3, 'Milk', 2.99);
--  INSERT INTO   SELECT
INSERT INTO products (product_no, name, price)
SELECT product_no, name, price FROM new_products WHERE release_date = 'today';
```
### UPDATE
```sql
UPDATE products SET price = 10 WHERE price = 5;

--
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
--
UPDATE
    TABLE_A ccscs
SET
    (old_value) =(
    SELECT
        a.new_vale
    FROM
        temp_data a
    WHERE
        ccscs.key = a.key
    )
WHERE
    EXISTS (
    SELECT
        1
    FROM
         temp_data a
    WHERE
        ccscs.key = a.key );  
```

### DELETE
```sql
DELETE FROM products WHERE price = 10;
```
### TRUNCATE 
```sql
TRUNCATE TABLE  table_name;
```
### RETURNING
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
## 查询

### LIKE
```sql
-- 找出 SALARY 字段中以 200 开头的数据
select * from A
WHERE SALARY::text LIKE '200%'	
-- 找出 SALARY 字段中含有 200 字符的数据
WHERE SALARY::text LIKE '%200%'	
-- 找出 SALARY 字段中在第二和第三个位置上有 00 的数据
WHERE SALARY::text LIKE '_00%'	
-- 找出 SALARY 字段中以 2 开头的字符长度大于 3 的数据
WHERE SALARY::text LIKE '2_%_%'	
-- 找出 SALARY 字段中以 2 结尾的数据
WHERE SALARY::text LIKE '%2'	
-- 找出 SALARY 字段中 2 在第二个位置上并且以 3 结尾的数据
WHERE SALARY::text LIKE '_2%3'	
--找出 SALARY 字段中以 2 开头，3 结尾并且是 5 位数的数据
WHERE SALARY::text LIKE '2___3'	
```

### LIMIT 
```sql
SELECT column1, column2, columnN 
FROM table_name
LIMIT [no of rows] OFFSET [row num]
-- 读取 4 条数据
SELECT * FROM COMPANY LIMIT 4;
-- 从第三位开始提取 3 个记录
SELECT * FROM COMPANY LIMIT 3 OFFSET 2;
```

### ORDER 
```sql
SELECT column-list
FROM table_name
[WHERE condition]
[ORDER BY column1, column2, .. columnN] [ASC | DESC];
-- ASC 表示升序，DESC 表示降序
-- desc nulls first
-- desc nulls last
```

### GROUP BY 
```sql
SELECT column-list
FROM table_name
WHERE [ conditions ]
GROUP BY column1, column2....columnN
--
SELECT NAME, SUM(SALARY) FROM COMPANY GROUP BY NAME;
```

### GROUPING SETS
```sql
SELECT *FROM develop.items_sold is2 ;

|brand               |sold_size |sales |
|--------------------|----------|------|
|Foo                 |L         |10    |
|Foo                 |M         |20    |
|Bar                 |M         |15    |
|Bar                 |L         |5     |


SELECT	brand,	sold_size,	SUM(sales)FROM	develop.items_sold is2
GROUP BY	GROUPING SETS ((brand),	(sold_size),	());

|brand               |sold_size |sum                 |
|--------------------|----------|--------------------|
|                    |          |50                  |
|Foo                 |          |30                  |
|Bar                 |          |20                  |
|                    |L         |15                  |
|                    |M         |35                  |
```
### ROLLUP
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
### JOIN

```sql
-- 内连接
SELECT table1.column1, table2.column2...
FROM table1
INNER JOIN table2
ON table1.common_filed = table2.common_field;
-- 左外连接
SELECT ... FROM table1 LEFT OUTER JOIN table2 ON conditional_expression ...

-- 右外连接
SELECT ... FROM table1 RIGHT OUTER JOIN table2 ON conditional_expression ...
-- 外连接
SELECT ... FROM table1 FULL OUTER JOIN table2 ON conditional_expression ...
```

### UNION

```sql
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]

UNION

SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]

--UNION ALL 操作符可以连接两个有重复行的 SELECT 语句
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
UNION ALL
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
```

## NULL
```sql
select case when coalesce(name,'') = '' then '姓名为空' else name end from student
```

### VALUES
```sql
SELECT * FROM (VALUES (1, 'one'), (2, 'two'), (3, 'three')) AS t (num,letter);
|num        |letter                                                                                              |
|-----------|----------------------------------------------------------------------------------------------------|
|1          |one                                                                                                 |
|2          |two                                                                                                 |
|3          |three                                                                                               |

```

### 递归查询
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
```

### 分组查询

```sql
-- desc nulls first
-- desc nulls last
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

```

### 数字
```sql
-- regexp_replace
SELECT
	REGEXP_REPLACE(TRIM(TO_CHAR(num, '999D99')) , '(?<=\.\d*)0+$|\.0*$', '') num,letter
FROM	(VALUES (100.00,'one'),(100.10,'two'),(100.11,'three')) AS t (num,letter);

100	one
100.1	two
100.11	three
```