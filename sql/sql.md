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
SELECT *
FROM (
	SELECT emp.*, ROW_NUMBER() OVER (PARTITION BY deptno ORDER BY ROWNUM) AS cn
	FROM emp
)
WHERE cn = 1;
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
