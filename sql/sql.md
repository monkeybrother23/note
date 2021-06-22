# 表

```sql
--修改表名 
ALTER TABLE old_name RENAME new_name;
--添加表列 
ALTER TABLE sys_menu ADD COLUMN `version` INT ( 5 ) NOT NULL COMMENT '数据版本号';
ALTER TABLE sys_menu ADD COLUMN `create_time` datetime NOT NULL COMMENT '创建时间';
ALTER TABLE sys_menu ADD COLUMN `create_by` VARCHAR ( 32 ) NOT NULL COMMENT '创建人';
ALTER TABLE sys_menu ADD COLUMN `update_time` datetime NOT NULL COMMENT '更新时间';
ALTER TABLE sys_menu ADD COLUMN `update_by` VARCHAR ( 32 ) NOT NULL COMMENT '更新人';
--删除表列 ALTER TABLE test DROP COLUMN del_name;
--修改表列类型 ALTER TABLE test MODIFY address CHAR (10 )
```

# 查询
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

## 排序
```sql
--自定义排序(Oracle)
SELECT *
FROM (
	SELECT 'A' AS name
	FROM dual
	UNION
	SELECT 'B' AS name
	FROM dual
	UNION
	SELECT 'C' AS name
	FROM dual
)
ORDER BY decode(name, 'B', 1, 'A', 2, 'C', 3, 4)
```
