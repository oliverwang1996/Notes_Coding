# 实战笔记_1

[TOC]



### 1. **SQL 语句的基本结构**

SQL 语句通常由以下几个部分组成：
- **关键词**（如`SELECT`、`INSERT`、`UPDATE`、`DELETE`等），用于指定要执行的操作。
- **操作对象**（如表名或列名），指定要操作的数据库表或字段。
- **条件**（如`WHERE`子句），用于筛选或指定数据。

### 2. **常见的SQL语句**
- **SELECT 语句**：用于从一个或多个表中查询数据。
  ```sql
  SELECT column1, column2, ...
  FROM table_name
  WHERE condition;
  ```
  - `SELECT` 后面跟着你想要查询的列名。
  - `FROM` 指定要查询的表。
  - `WHERE` 用于筛选条件，返回满足条件的记录。

- **INSERT 语句**：用于向表中插入新的数据行。如果有重复，可以用REPLACE INTO
  
  ```sql
  INSERT INTO table_name (column1, column2, ...)
  VALUES (value1, value2, ...);
  ```
  - `INSERT INTO` 指定要插入数据的表及对应的列。
  - `VALUES` 指定要插入的值。
  
- **UPDATE 语句**：用于更新表中已存在的数据。
  ```sql
  UPDATE table_name
  SET column1 = value1, column2 = value2, ...
  WHERE condition;
  ```
  - `UPDATE` 指定要更新的表。
  - `SET` 指定要更新的列和对应的值。
  - `WHERE` 用于指定哪些记录需要被更新。

- **DELETE 语句**：用于删除表中的数据行。
  ```sql
  DELETE FROM table_name
  WHERE condition;
  ```
  - `DELETE FROM` 指定要删除数据的表。
  - `WHERE` 指定要删除的条件。

### 3. **常用的SQL关键字和操作**
- **`DISTINCT`**：用于返回唯一不同的值。
  ```sql
  SELECT DISTINCT column1 FROM table_name;
  ```

- **`ORDER BY`**：用于对结果集进行排序。
  ```sql
  SELECT column1, column2
  FROM table_name
  ORDER BY column1 ASC|DESC;
  ```

- **`GROUP BY`**：用于根据一个或多个列对结果集进行分组，常与聚合函数（如`COUNT`、`SUM`、`AVG`等）一起使用。
  ```sql
  SELECT column1, COUNT(*)
  FROM table_name
  GROUP BY column1;
  ```

- **`HAVING`**：用于筛选分组后的结果集。
  ```sql
  SELECT column1, COUNT(*)
  FROM table_name
  GROUP BY column1
  HAVING COUNT(*) > 1;
  ```

- **`JOIN`**：用于在两个或多个表之间建立连接并查询数据。
  ```sql
  SELECT column1, column2
  FROM table1
  INNER JOIN table2
  ON table1.common_column = table2.common_column;
  ```
  - 常见的 `JOIN` 类型包括：`INNER JOIN`、`LEFT JOIN`、`RIGHT JOIN`、`FULL JOIN`。

### 4. **SQL 中的条件和逻辑操作**
- **`AND` 和 `OR`**：用于组合多个条件。
  
  ```sql
  SELECT * FROM table_name WHERE condition1 AND condition2;
  ```
  
- **`IN`**：用于匹配多个可能的值。
  ```sql
  SELECT * FROM table_name WHERE column1 IN (value1, value2, ...);
  ```

- **`BETWEEN`**：用于匹配一个范围内的值。
  ```sql
  SELECT * FROM table_name WHERE column1 BETWEEN value1 AND value2;
  ```

- **`LIKE`**：用于匹配特定模式的值，常用于搜索类似的值。
  ```sql
  SELECT * FROM table_name WHERE column1 LIKE 'pattern%';
  ```

- **`IS NULL`**：用于检查某个列是否为`NULL`。
  ```sql
  SELECT * FROM table_name WHERE column1 IS NULL;
  ```

### 5. **聚合函数**
- **`COUNT`**：返回符合条件的行数。
- **`SUM`**：返回数值列的总和。
- **`AVG`**：返回数值列的平均值。
- **`MAX` 和 `MIN`**：返回数值列的最大值和最小值。

### 6. **子查询（Subquery）**
- 子查询是在另一个查询中嵌套的查询，通常用于复杂的查询。
  ```sql
  SELECT column1
  FROM table_name
  WHERE column2 = (SELECT MAX(column2) FROM table_name);
  ```

这些是SQL语言的基础，掌握它们能让你处理大部分的数据库查询任务。随着学习的深入，你会遇到更多高级的SQL功能和优化技巧。



在深入理解SQL语法之前，了解SQL语句的执行规则（也称为执行顺序）是非常重要的。SQL的执行顺序决定了查询的具体处理方式。以下是SQL语法的执行顺序及一些关键概念的深入解析：



# 实战笔记_2

### 1. **SQL 语法的执行顺序**

虽然我们通常按照`SELECT -> FROM -> WHERE -> GROUP BY -> HAVING -> ORDER BY`的顺序编写SQL语句，但SQL的执行顺序实际上不同。通常情况下，SQL语句的执行顺序如下：

1. **FROM**：首先选择并连接要操作的表或视图。
2. **ON**：在连接操作中执行连接条件。
3. **JOIN**：执行表之间的连接操作。
4. **WHERE**：在表连接完成后筛选记录。
5. **GROUP BY**：将记录按照指定列进行分组。
6. **HAVING**：对分组后的记录进行过滤。
7. **SELECT**：选择指定的列。
8. **DISTINCT**：去除重复的记录。
9. **ORDER BY**：对结果集进行排序。
10. **LIMIT/OFFSET**：限制返回的记录行数，或跳过一定数量的记录行。

### 2. **INNER JOIN 的概念**
`INNER JOIN` 是一种连接操作，用于将两张表中的记录进行匹配，并返回所有匹配的记录。`INNER`的作用是确保只返回两张表中都存在的记录（即“内部”的匹配），不匹配的记录则被过滤掉。

```sql
SELECT table1.column1, table2.column2
FROM table1
INNER JOIN table2
ON table1.common_column = table2.common_column;
```

在这里，`INNER`强调了连接的严格性，确保只返回存在于两张表中的匹配记录。如果省略`INNER`关键字，默认的`JOIN`操作也是`INNER JOIN`。

### 3. **更多的聚合函数**
除了常用的`COUNT`、`SUM`、`AVG`、`MAX`、`MIN`，还有一些其他的聚合函数：

- **`COUNT(DISTINCT column)`**：计算某列中不重复值的个数。
- **`GROUP_CONCAT()`**：将组内的字段值连接成一个字符串（仅适用于某些数据库，如MySQL）。
- **`STDDEV()`**：计算一组值的标准差。
- **`VARIANCE()`**：计算一组值的方差。

### 4. **子查询的高级用法**
子查询（Subquery）是嵌套在另一条SQL语句中的查询。子查询可以出现在`SELECT`、`FROM`、`WHERE`、`HAVING`等部分。

#### **4.1 标量子查询**
返回单个值，可以用于任何需要单值的地方：
```sql
SELECT column1,
       (SELECT MAX(column2) FROM table2 WHERE table1.column3 = table2.column3) AS max_value
FROM table1;
```

#### **4.2 表子查询**
返回一个表，可以用在`FROM`子句中：
```sql
SELECT subquery_table.column1
FROM (SELECT column1, column2 FROM table2 WHERE condition) AS subquery_table;
```

#### **4.3 相关子查询**
子查询依赖于外部查询中的某一列值。每次外部查询处理一行时，相关子查询都会执行一次：
```sql
SELECT column1
FROM table1
WHERE column2 > (SELECT AVG(column2) FROM table2 WHERE table2.column3 = table1.column3);
```

#### **4.4 EXISTS 子查询**
用于检查子查询是否返回至少一行记录。若子查询返回行，则`EXISTS`为`TRUE`：
```sql
SELECT column1
FROM table1
WHERE EXISTS (SELECT 1 FROM table2 WHERE table2.column3 = table1.column3);
```

### 5. **Inner Join 的深层次理解**
在数据库优化中，`INNER JOIN`通常会进行条件筛选（`ON`条件）以及筛选后的结果集联结，这样做可以减少不必要的记录，提升查询效率。`INNER`一词则强调了这种“内部”匹配的特性。



# 实战笔记_三

在SQL中，确实存在与`INNER JOIN`相对的`OUTER JOIN`，具体细分为`LEFT JOIN`、`RIGHT JOIN`和`FULL OUTER JOIN`。这些都是SQL的进阶知识，掌握它们对精通SQL非常重要。此外，还有许多其他高级概念和技术可以帮助你更好地掌握SQL。以下是详细介绍：

### 1. **OUTER JOIN**
`OUTER JOIN` 用于返回在连接表中匹配与不匹配的记录。它的具体类型如下：

#### **1.1 LEFT JOIN（或 LEFT OUTER JOIN）**
返回左表中的所有记录，即使右表中没有匹配项，也会返回左表记录并填充NULL。
```sql
SELECT table1.column1, table2.column2
FROM table1
LEFT JOIN table2
ON table1.common_column = table2.common_column;
```

#### **1.2 RIGHT JOIN（或 RIGHT OUTER JOIN）**
返回右表中的所有记录，即使左表中没有匹配项，也会返回右表记录并填充NULL。
```sql
SELECT table1.column1, table2.column2
FROM table1
RIGHT JOIN table2
ON table1.common_column = table2.common_column;
```

#### **1.3 FULL OUTER JOIN**
返回左右两表中的所有记录，不论是否匹配。匹配不上时，用NULL填充。
```sql
SELECT table1.column1, table2.column2
FROM table1
FULL OUTER JOIN table2
ON table1.common_column = table2.common_column;
```

### 2. **窗口函数（Window Functions）**
窗口函数在关系数据库中非常强大，它们用于对结果集的子集进行计算，而不需要进行分组。常用的窗口函数包括：

- **`ROW_NUMBER()`**：为结果集的每一行分配唯一的行号。
- **`RANK()`**：为结果集中的行分配排名，相同的值会有相同的排名，并且排名中会有跳跃。
- **`DENSE_RANK()`**：类似于`RANK()`，但没有跳跃。
- **`NTILE(n)`**：将结果集划分为n个部分，并为每行分配一个部分编号。
- **`LEAD()`和`LAG()`**：访问结果集中某行的后/前一行的数据。
- **`SUM() OVER`**：计算当前行及其之前所有行的总和。

例如：
```sql
SELECT column1, column2,
       ROW_NUMBER() OVER(PARTITION BY column1 ORDER BY column2) AS row_num
FROM table_name;
```

### 3. **公共表表达式（CTE）**
CTE（Common Table Expressions）是定义临时结果集的一种方式，可以让复杂查询变得更清晰和可读。CTE通常用于递归查询、简化复杂的嵌套查询，或是分阶段处理数据。

```sql
WITH cte_name AS (
    SELECT column1, column2
    FROM table_name
    WHERE condition
)
SELECT column1, column2
FROM cte_name
WHERE another_condition;
```

### 4. **递归查询**
递归查询常用于处理层次结构数据（如员工和经理的层级关系）。通过使用CTE的递归功能，可以逐步构建这样的层次结构。

```sql
WITH RECURSIVE cte_name AS (
    SELECT column1, column2
    FROM table_name
    WHERE condition
    
    UNION ALL
    
    SELECT t.column1, t.column2
    FROM table_name t
    INNER JOIN cte_name c ON t.parent_id = c.id
)
SELECT * FROM cte_name;
```

### 5. **索引与性能优化**
了解如何创建并使用索引可以极大地提高查询效率。索引是数据库为快速查找而维护的附加数据结构，它们可以显著加快`SELECT`查询，但过多的索引也可能影响`INSERT`、`UPDATE`和`DELETE`的性能。

- **创建索引**：`CREATE INDEX index_name ON table_name(column_name);`

- **索引的类型**：如`B-Tree`索引、`Hash`索引、`Full-text`索引等。

- **查询优化**：使用`EXPLAIN`或`EXPLAIN ANALYZE`来分析查询计划。

  ````sql
  索引创建、删除与使用：
  
  - 1.1 create方式创建索引：
  
  ```
  CREATE`` ``[UNIQUE -- 唯一索引`` ``| FULLTEXT -- 全文索引`` ``] INDEX index_name ON table_name -- 不指定唯一或全文时默认普通索引`` ``(column1[(length) [DESC|ASC]] [,column2,...]) -- 可以对多列建立组合索引 
  ```
  
  - 1.2 alter方式创建索引：`ALTER TABLE tb_name ADD [UNIQUE | FULLTEXT] [INDEX] index_content(content)`
  - 2.1 drop方式删除索引：`DROP INDEX <索引名> ON <表名>`
  - 2.2 alter方式删除索引：`ALTER TABLE <表名> DROP INDEX <索引名>`
  - 3.1 索引的使用：
    - 索引使用时满足最左前缀匹配原则，即对于组合索引(col1, col2)，在不考虑引擎优化时，条件必须是col1在前col2在后，或者只使用col1，索引才会生效；
    - 索引不包含有NULL值的列
    - 一个查询只使用一次索引，where中如果使用了索引，order by就不会使用
    - like做字段比较时只有前缀确定时才会使用索引
    - 在列上进行运算后不会使用索引，如year(start_time)<2020不会使用start_time上的索引
  ````

  

### 6. **事务（Transactions）与锁定（Locking）**
事务是一个由多个SQL语句组成的逻辑操作单元，事务确保了数据库的一致性。主要特性包括ACID（原子性、一致性、隔离性、持久性）。

- **启动事务**：`BEGIN TRANSACTION;`
- **提交事务**：`COMMIT;`
- **回滚事务**：`ROLLBACK;`

锁定机制用于控制并发访问，防止数据的竞争条件。常见的锁有共享锁（`SHARE LOCK`）和排他锁（`EXCLUSIVE LOCK`）。

### 7. **高级子查询**
进一步理解子查询的不同用途，例如：

- **多层嵌套子查询**：用于非常复杂的查询需求。
- **跨表子查询**：在主查询的`FROM`或`WHERE`中引用多个表的数据。
- **WITH 子句的联合**：可以使用多个CTE，以简化复杂查询。

### 8. **其他高级主题**
- **存储过程（Stored Procedures）和函数（Functions）**：用于封装复杂的业务逻辑。
- **触发器（Triggers）**：自动执行响应数据库事件的SQL代码。
- **视图（Views）**：虚拟表，用于简化查询或增强安全性。
- **分区（Partitioning）**：将表按某一字段划分为多个部分以提升性能。
- **数据库设计模式**：如范式化和反范式化。



2. 
