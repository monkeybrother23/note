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
INSERT INTO develop.items_sold (brand, sold_size, sales) VALUES('Foo', 'L', 10);
INSERT INTO develop.items_sold (brand, sold_size, sales) VALUES('Foo', 'M', 20);
INSERT INTO develop.items_sold (brand, sold_size, sales) VALUES('Bar', 'M', 15);
INSERT INTO develop.items_sold (brand, sold_size, sales) VALUES('Bar', 'L', 5);

SELECT
	brand,
	sold_size,
	SUM(sales)
FROM
	develop.items_sold is2
GROUP BY
	GROUPING SETS ((brand),
	(sold_size),
	());
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

### CUBE 
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

### NULL
```sql
SELECT COALESCE (NULL ,'Y') a,COALESCE ('N' ,'Y') b;
```

### VALUES

```sql
SELECT * FROM (VALUES (1, 'one'), (2, 'two'), (3, 'three')) AS t (num,letter);
```

### 递归查询
```sql
-- DROP TABLE develop.employees;
CREATE TABLE develop.employees (
	employee_id int2 NOT NULL,
	full_name varchar NOT NULL,
	manager_id int4 NULL,
	CONSTRAINT employees_pkey PRIMARY KEY (employee_id)
);
-- INSER
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
-- SELECT
WITH RECURSIVE subordinates AS (
SELECT
	a.employee_id,
	a.full_name,
	a.manager_id
FROM
	employees a
WHERE
	a.employee_id = 1
UNION
SELECT
	e.employee_id,
	e.full_name,
	e.manager_id
FROM
	employees e
INNER JOIN subordinates s ON
	s.employee_id = e.manager_id )
SELECT
	*
FROM
	subordinates;
     
|employee_id|full_name           |manager_id |
|-----------|--------------------|-----------|
|1          |Michael North       |           |
|2          |Megan Berry         |1          |
|3          |Sarah Berry         |1          |
|4          |Zoe Black           |1          |
|5          |Tim James           |1          |
|6          |Bella Tucker        |2          |
|7          |Ryan Metcalfe       |2          |
|8          |Max Mills           |2          |
|9          |Benjamin Glover     |2          |
|10         |Carolyn Henderson   |3          |
|11         |Nicola Kelly        |3          |
|12         |Alexandra Climo     |3          |
|13         |Dominic King        |3          |
|14         |Leonard Gray        |4          |
|15         |Eric Rampling       |4          |
|16         |Piers Paige         |7          |
|17         |Ryan Henderson      |7          |
|18         |Frank Tucker        |8          |
|19         |Nathan Ferguson     |8          |
|20         |Kevin Rampling      |8          |
```

### 分组查询

```sql
-- DROP TABLE develop.student;
CREATE TABLE develop.student (
	student_name varchar(20) NOT NULL,
	score int2 NOT NULL,
	course int2 NULL
);

-- desc nulls first
-- desc nulls last
SELECT
	student_name ,
	score,
	course,
	ROW_NUMBER() OVER(PARTITION BY course
ORDER BY
	score DESC NULLS LAST) AS RANK
FROM
	develop.student;	
|student_name        |score |course|rank                |
|--------------------|------|------|--------------------|
|A                   |100   |1     |1                   |
|C                   |70    |1     |2                   |
|B                   |70    |1     |3                   |
|H                   |60    |1     |4                   |
|D                   |50    |1     |5                   |
|G                   |80    |2     |1                   |
|F                   |70    |2     |2                   |
|E                   |      |2     |3                   |

SELECT
	student_name ,
	score,
	course,
	RANK() OVER(PARTITION BY course
ORDER BY
	score DESC NULLS LAST) AS RANK
FROM
	develop.student;
--rank over () 可以把成绩相同的两名是并列，如下course = 1 的结果rank值为：1 2 2 4 5
|student_name        |score |course|rank                |
|--------------------|------|------|--------------------|
|A                   |100   |1     |1                   |
|C                   |70    |1     |2                   |
|B                   |70    |1     |2                   |
|H                   |60    |1     |4                   |
|D                   |50    |1     |5                   |
|G                   |80    |2     |1                   |
|F                   |70    |2     |2                   |
|E                   |      |2     |3                   |


--dense_rank()和rank over()很相似，可以把学生成绩并列不间断顺序排名，如下course = 1 的结果rank值为：1 2 2 3 4
SELECT
	student_name ,
	score,
	course,
	DENSE_RANK() OVER(PARTITION BY course
ORDER BY
	score DESC NULLS LAST) AS RANK
FROM
	develop.student;
|student_name        |score |course|rank                |
|--------------------|------|------|--------------------|
|A                   |100   |1     |1                   |
|C                   |70    |1     |2                   |
|B                   |70    |1     |2                   |
|H                   |60    |1     |3                   |
|D                   |50    |1     |4                   |
|G                   |80    |2     |1                   |
|F                   |70    |2     |2                   |
|E                   |      |2     |3                   |


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

## 索引

### 索引类型

#### 单一索引

```sql
CREATE INDEX index_name ON table_name (column_name);
```

#### 组合索引

```sql
CREATE INDEX index_name ON table_name (column1_name, column2_name);
```

#### 唯一索引

```sql
CREATE UNIQUE INDEX index_name on table_name (column_name);
```

#### 隐式索引

```sql
-- 隐式索引 是在创建对象时，由数据库服务器自动创建的索引。索引自动创建为主键约束和唯一约束
```

### USING

#### BTREE

```sql
CREATE INDEX name ON table USING BTREE (column);
-- 每种索引类型都有其适合的查询类型，缺省时，CREATE INDEX命令将创建B-Tree索引。
CREATE INDEX index_name ON table_name (column1_name, column2_name);
```

#### hash

```sql
-- USING hash
CREATE INDEX name ON table USING hash (column);
```

### DROP

```sql
-- DROP INDEX
DROP INDEX index_name;
```

