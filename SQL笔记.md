# 一、初阶知识点

## 1. 数据库概览

### 1.1 数据库类型
- **关系型数据库**：以二维表的形式存储和管理数据，适合结构化数据。主要特点包括事务支持、ACID 特性和强一致性。
  - **常见关系型数据库**：MySQL、PostgreSQL、Oracle、SQL Server。
- **非关系型数据库**：存储结构为键值对、文档或图等。适合存储海量、半结构化或无结构化数据。其特点是灵活的结构和高扩展性。
  - **常见非关系型数据库**：MongoDB（文档型）、Redis（键值对）、Cassandra（列存储）、Neo4j（图数据库）。

### 1.2 开源和非开源数据库
- **开源数据库**：源码公开，允许用户修改和再分发。通常用于快速开发和学习。
  - 例子：MySQL、PostgreSQL、MariaDB、SQLite。
- **非开源数据库**：通常提供商业支持和企业级功能，需要付费使用。
  - 例子：Oracle、Microsoft SQL Server、IBM DB2。

### 1.3 MySQL 特点
- **跨平台性**：支持多种操作系统，适用于 Linux、Windows、macOS 等。
- **多语言支持**：提供多种语言的 API 接口，包括 C、C++、Java、Python 等。
- **存储引擎**：支持多种存储引擎（如 InnoDB、MyISAM 等），满足不同的存储需求。
- **事务支持**：支持事务、锁机制、外键等，保证数据一致性。

---

## 2. SQL 基础知识

### 2.1 SQL 基本语法
- **SQL 语句结构**：SQL 语句用于操作关系型数据库，包括数据查询、更新、插入和删除等操作。
- **常见 SQL 语句**：
  - **DDL**（数据定义语言）：`CREATE`、`DROP`、`ALTER`。
  - **DML**（数据操作语言）：`SELECT`、`INSERT`、`UPDATE`、`DELETE`。
  - **DCL**（数据控制语言）：`GRANT`、`REVOKE`。
  - **TCL**（事务控制语言）：`COMMIT`、`ROLLBACK`、`SAVEPOINT`。

### 2.2 SQL 数据库操作
- **创建、删除数据库**：
  ```sql
  CREATE DATABASE database_name;
  DROP DATABASE database_name;
  ```
- **查看数据库**：使用 `SHOW DATABASES;` 查看所有数据库。
- **切换数据库**：使用 `USE database_name;` 切换到指定数据库。

### 2.3 数据表管理
- **创建、修改、删除数据表**：
  ```sql
  CREATE TABLE table_name (column_name datatype [constraints], ...);
  ALTER TABLE table_name ADD column_name datatype;
  DROP TABLE table_name;
  ```
- **查看表结构**：使用 `DESC table_name;` 查看表结构。

### 2.4 数据操作
- **数据插入、更新和删除**：
  ```sql
  INSERT INTO table_name (column1, column2) VALUES (value1, value2);
  UPDATE table_name SET column1 = value1 WHERE condition;
  DELETE FROM table_name WHERE condition;
  ```
- **基本查询**：
  - 使用 `SELECT column1, column2 FROM table_name WHERE condition;` 查询数据。
  - **示例**：查找年龄大于 30 的所有员工。
    ```sql
    SELECT name, age FROM employees WHERE age > 30;
    ```

---



# 二、SQL 进阶知识

## 各类函数

### 1. 排序函数（Window Functions）

#### 1.1 常用排序函数

- **`RANK()`**：按指定列排序，给出排名。排名中如果有重复值，跳过后续排名。例如：1、2、2、4、4、6。
  
- **`ROW_NUMBER()`**：按指定列排序，并给每一行唯一的排名。不会跳过排名序列。例如：1、2、3、4、5、6。
  
- **`DENSE_RANK()`**：类似于 `RANK()`，但不会跳过排名序列。相同值排名相同，下一个不同值排名直接递增。例如：1、2、2、3、3、4。
  
- **`PERCENT_RANK()`**：计算排名在分组中的百分比。公式为：`(rank - 1) / (total_rows - 1)`。
  
- **`NTILE(n)`**：将排序结果平均分为 n 组，并给出每一行对应的分组编号。

#### 1.2 窗口函数中的偏移函数

- **`LEAD(column, n)`**：返回当前行向后第 n 行的值。用来在结果集中比较相邻行的数据。
  
- **`LAG(column, n)`**：返回当前行向前第 n 行的值。用来在结果集中比较前后行的数据。

#### 示例

```sql
SELECT 
    employee_id, 
    department, 
    salary,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank,
    DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dense_rank,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS row_number,
    PERCENT_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS percent_rank,
    NTILE(4) OVER (ORDER BY salary DESC) AS quartile
FROM employees;
```

在上述查询中，每个员工在其部门中将获得一个按薪水排序的 `rank`、`dense_rank`、`row_number`、`percent_rank` 及其分组位置 `quartile`。

---

### 2. 文本处理（String Manipulation）

#### 2.1 常用函数

- **`UPPER(text)`**：将字符串转换为大写。
- **`LOWER(text)`**：将字符串转换为小写。
- **`CONCAT(text1, text2, ...)`**：拼接多个字符串。
- **`SUBSTRING(text, start, length)`**：从字符串中提取子字符串，从 `start` 开始，长度为 `length`。
- **`REPLACE(text, old_text, new_text)`**：替换字符串中指定内容。
- **`TRIM(text)`**：去除字符串两端的空格。
- **`LPAD(text, length, pad_string)`**：在左侧填充字符串到指定长度。
- **`RPAD(text, length, pad_string)`**：在右侧填充字符串到指定长度。

#### 2.2 正则表达式（Regex）

在 SQL 中，尤其是 MySQL，可以使用 **REGEXP** 或 **RLIKE** 操作符进行正则匹配：

| 模式     | 描述                                            |
| -------- | ----------------------------------------------- |
| `^`      | 匹配字符串开头                                  |
| `$`      | 匹配字符串结尾                                  |
| `.`      | 匹配除 "\n" 外的任意单字符                      |
| `[...]`  | 匹配括号中的任意一个字符，如`[abc]` 匹配 'a'    |
| `[^...]` | 匹配括号中未包含的任意字符，如`[^abc]`匹配 'd'  |
| `p1|p2`  | 匹配 p1 或 p2                                   |
| `*`      | 匹配零次或多次前面的子表达式，如 `zo*` 匹配 `z` |
| `+`      | 匹配一次或多次前面的子表达式                    |
| `{n}`    | 匹配前一个元素 n 次                             |
| `{n,m}`  | 匹配前一个元素至少 n 次，最多 m 次              |

1. **字符串处理**

   常用字符串函数：

   - **UPPER()/LOWER()**：转换为大/小写
   - **CONCAT()**：拼接字符串
   - **SUBSTRING()**：提取子字符串
   - **TRIM()**：去除字符串两端的空白或指定字符
   - **REPLACE()**：替换字符串中的指定部分
   - **LENGTH()**：返回字符长度

   ```sql
   SELECT CONCAT(UPPER(first_name), ' ', LOWER(last_name)) AS formatted_name
   FROM users;
   ```

2. **分词和拼接**

   通过 **SUBSTRING_INDEX()** 和 **REGEXP_SUBSTR()** 可以实现分词操作：

   ```sql
   SELECT SUBSTRING_INDEX(profile, ',', -1) AS gender
   FROM user_submit;
   ```

3. **拼接**

   使用 **CONCAT** 和 **CONCAT_WS** 实现字符串拼接，避免 NULL 值影响拼接：

   ```sql
   SELECT CONCAT_WS(' ', first_name, last_name) AS full_name
   FROM users;
   ```

### 3. 日期处理（Date Manipulation）

#### 3.1 常用函数

- **`CURDATE()`**：返回当前日期。
- **`NOW()`**：返回当前日期和时间。
- **`DATE_FORMAT(date, format)`**：格式化日期。
- **`DATEDIFF(date1, date2)`**：计算两个日期间的差值（以天为单位）。
- **`DATE_ADD(date, INTERVAL expr unit)`**：给日期加上时间间隔。
- **`YEAR(date)`**、**`MONTH(date)`**、**`DAY(date)`**：分别返回年份、月份和日。

#### 3.2 日期处理示例

```sql
SELECT 
    CURDATE() AS today,
    NOW() AS current_datetime,
    DATE_FORMAT(order_date, '%Y-%m-%d') AS formatted_date,
    DATEDIFF(NOW(), order_date) AS days_since_order,
    DATE_ADD(order_date, INTERVAL 7 DAY) AS one_week_later
FROM orders;
```

在这里，可以获得订单日期距今天的天数，或得到未来7天的日期。

---

### 4. 合并查询（Union 与 Join）

#### 4.1 UNION 操作符

- **`UNION`**：用于合并两个 `SELECT` 查询的结果，并去除重复项。
- **`UNION ALL`**：合并两个 `SELECT` 查询的结果，不去重，性能优于 `UNION`。

  ```sql
  SELECT name FROM employees
  UNION
  SELECT name FROM contractors;
  ```

#### 4.2 Join 操作符

- **`INNER JOIN`**：只返回在两个表中匹配的记录。
- **`LEFT JOIN`**：返回左表中的所有记录，以及匹配的右表记录；如果没有匹配，则右表值为 `NULL`。
- **`RIGHT JOIN`**：返回右表中的所有记录，以及匹配的左表记录；如果没有匹配，则左表值为 `NULL`。
- **`FULL OUTER JOIN`**：返回两个表中的所有记录，没有匹配时返回 `NULL`（MySQL 中通过 `LEFT JOIN` 和 `UNION` 组合模拟实现）。
  
  ```sql
  SELECT e.name, d.department_name 
  FROM employees e
  LEFT JOIN departments d ON e.department_id = d.department_id;
  ```

#### 4.3 Join 使用注意事项

1. `JOIN` 时，字段名重复需要明确指出表名。
2. `JOIN` 语句的顺序可能影响查询结果和性能。
3. 对于大型数据集，尽量使用 `INNER JOIN` 代替 `OUTER JOIN` 以提高性能。

---

### 5. 聚合与分组（Aggregation & Grouping）

#### 5.1 聚合函数

- **`COUNT(column)`**：计算列中非 `NULL` 值的数量。
- **`SUM(column)`**：计算列中数值之和。
- **`AVG(column)`**：计算列中数值的平均值。
- **`MAX(column)`**、**`MIN(column)`**：计算列中的最大和最小值。

#### 5.2 分组与筛选

- **`GROUP BY`**：将查询结果按指定列进行分组。
- **`HAVING`**：对分组结果进行过滤。

  ```sql
  SELECT department, COUNT(employee_id) AS employee_count
  FROM employees
  GROUP BY department
  HAVING COUNT(employee_id) > 5;
  ```

#### 5.3 ONLY_FULL_GROUP_BY 模式

- 如果遇到 `ONLY_FULL_GROUP_BY` 错误，可以通过使用 `ANY_VALUE()` 函数绕过限制。
  
  ```sql
  SELECT department, ANY_VALUE(name), COUNT(*)
  FROM employees
  GROUP BY department;
  ```

---

### 6. 数据库连接（Join）类型

#### 6.1 各种 JOIN 操作

1. **内连接（INNER JOIN）**：只返回匹配的记录。
2. **左连接（LEFT JOIN）**：返回左表的所有记录及右表中匹配的记录。
3. **右连接（RIGHT JOIN）**：返回右表的所有记录及左表中匹配的记录。
4. **全连接（FULL OUTER JOIN）**：返回两个表中所有记录，没有匹配时返回 `NULL`（MySQL 通过 `LEFT JOIN` 和 `UNION` 实现）。

#### 6.2 使用 UNION 来模拟全连接

在 MySQL 中，可以通过以下方式模拟 `FULL OUTER JOIN`：

```sql
SELECT a.*, b.* 
FROM table_a a 
LEFT JOIN table_b b ON a.id = b.id
UNION 
SELECT a.*, b.* 
FROM table_a a 
RIGHT JOIN table_b b ON a.id = b.id;
```

### 7. 连接和筛选的顺序

SQL 语句的执行顺序并非从 `SELECT` 开始，而是按如下步骤：

1. **FROM**：选择表和视图
2. **JOIN**：执行连接操作
3. **WHERE**：过滤记录
4. **GROUP BY**：分组
5. **HAVING**：过滤分组后结果
6. **SELECT**：选择返回列
7. **ORDER BY**：排序结果
8. **LIMIT**：限制返回的行数

```sql
Select department, COUNT(*) AS employee_count
FROM employees
WHERE salary > 50000
GROUP BY department
HAVING employee_count > 1
ORDER BY employee_count DESC;
```

### 8. **子查询的高级用法**

子查询（Subquery）是嵌套在另一条SQL语句中的查询。子查询可以出现在`SELECT`、`FROM`、`WHERE`、`HAVING`等部分。

#### **8.1 标量子查询**

返回单个值，可以用于任何需要单值的地方：

```sql
SELECT column1,
       (SELECT MAX(column2) FROM table2 WHERE table1.column3 = table2.column3) AS max_value
FROM table1;
```

#### **8.2 表子查询**

返回一个表，可以用在`FROM`子句中：

```sql
SELECT subquery_table.column1
FROM (SELECT column1, column2 FROM table2 WHERE condition) AS subquery_table;
```

#### **8.3 相关子查询**

子查询依赖于外部查询中的某一列值。每次外部查询处理一行时，相关子查询都会执行一次：

```sql
SELECT column1
FROM table1
WHERE column2 > (SELECT AVG(column2) FROM table2 WHERE table2.column3 = table1.column3);
```

#### **8.4 EXISTS 子查询**

用于检查子查询是否返回至少一行记录。若子查询返回行，则`EXISTS`为`TRUE`：

```sql
SELECT column1
FROM table1
WHERE EXISTS (SELECT 1 FROM table2 WHERE table2.column3 = table1.column3);
```

### 9. 其他笔记

1. **`WHERE` 和 `HAVING` 的区别**：

   1. **`WHERE` 子句**：用于在数据聚合之前筛选记录。它只能应用于具体的列值，而不能用于聚合函数。
   2. **`HAVING` 子句**：用于对分组或聚合结果进行筛选。可以使用聚合函数，如 `SUM`、`COUNT` 等。

2. 使用`COUNT`等函数需要用`GROUP BY`，`GROUP BY` 后面可接 `HAVING`。`COUNT` 函数不能在 `WHERE` 子句中使用，也不能直接对函数结果进行计数，因为 `COUNT` 函数应该用于汇总之后的结果。

3. `COUNT()` 可以用多个条件

   ```sql
   '计算2021年每个月里试卷作答区用户平均月活跃天数avg_active_days和月度活跃人数mau'
   SELECT
       DATE_FORMAT(submit_time, '%Y%m') AS monthtime,
       ROUND(COUNT(DISTINCT uid, date_format(submit_time,'%Y%m%d'))/COUNT(DISTINCT uid),2) AS avg_active_days,
       '这里count要用2个条件，且要按天数来确定'
       COUNT(DISTINCT uid) AS mau
   FROM    
       exam_record
   WHERE  
       submit_time IS NOT NULL AND 
       year(submit_time) = 2021
   GROUP BY
       monthtime
   ```

4. `Limit` 可以用在查询也可以用在删除。分页展示，**查看第n页数据，每页m条：**`Limit` ``6``,``3``=》 `Limit` (n-``1``)*m,m，查看第n页数据，每页m条的公式，该部分实际上是LIMIT (``3``-``1``)*``3``,``3``。 

5. 关于解决最新的SQL版本中`ONLY_FULL_GROUP_BY`报错的办法：`ONLY_FULL_GROUP_BY`的语义就是确定select 中的所有列的值要么是来自于聚合函数（sum、avg、max等）的结果，要么是来自于`group by list`中的表达式的值。MySQL提供了`any_value()`函数来抑制`ONLY_FULL_GROUP_BY`值被拒绝。所以只需要在非group by的列上加`any_value()`就可以了。

6. 全部删除（表清空，包含自增计数器重置）：`TRUNCATE tb_name`

## 其他知识点

### 1.  数据库对象与管理

#### 1.1 视图（View）

- **视图**：虚拟表，从一个或多个表中获取数据，简化查询、保护数据安全。
  - 视图本身不存储数据，而是存储 `SELECT` 语句，调用视图时再执行查询。
  
  **示例**：创建显示高工资员工的视图。
  ```sql
  CREATE VIEW high_salary_employees AS 
  SELECT name, salary 
  FROM employees 
  WHERE salary > 80000;
  ```

#### 1.2 存储过程与函数（Stored Procedures & Functions）
- **存储过程**：预定义的 SQL 语句集，通常用于执行复杂操作或批量任务。
- **函数**：可以返回值，用于复杂计算或封装业务逻辑。
  
  **示例**：创建一个存储过程，根据部门 ID 查询员工信息。
  ```sql
  DELIMITER //
  CREATE PROCEDURE GetEmployeesByDept(dept_id INT)
  BEGIN
      SELECT * FROM employees WHERE department_id = dept_id;
  END //
  DELIMITER ;
  ```

#### 1.3 索引（Index）
- **索引**：用于加速数据检索，类似于书籍的目录。常见索引包括：
  - 普通索引：加速数据检索，允许重复和 NULL。
  - 唯一索引：保证数据唯一性

  - 主键索引：唯一且不可为空，用于标识表中的唯一记录。

  **示例**：为员工姓名和部门创建复合索引。
  ```sql
  CREATE INDEX idx_name_department ON employees(name, department);
  ```

#### 1.4 用户与权限管理
- **创建和管理用户**：控制数据库访问权限。
  - 使用 `CREATE USER` 创建用户。
  - 使用 `GRANT` 授权，`REVOKE` 撤销权限。
  
  **示例**：创建用户并授予 SELECT 权限。
  ```sql
  CREATE USER 'reporting_user'@'localhost' IDENTIFIED BY 'password123';
  GRANT SELECT ON sales_db.* TO 'reporting_user'@'localhost';
  ```

---

### 2. SQL 优化与调优

#### 2.1 SQL 优化
- **索引优化**：常用索引字段如 `WHERE`、`JOIN`、`ORDER BY`、`GROUP BY` 中出现的字段。
- **查询优化**：合理使用 `LIMIT`、避免 `SELECT *`、使用 EXISTS 替代 COUNT 等。
- **使用 `EXPLAIN`**：分析查询性能，查看查询计划。
  
  **示例**：分析查询计划，评估查询效率。
  ```sql
  EXPLAIN SELECT name FROM employees WHERE department = 'HR' AND age > 30;
  ```

#### 2.2 数据库优化
- **存储引擎选择**：选择合适的存储引擎以匹配使用场景。例如：
  - `InnoDB` 适合高并发写操作。
  - `MyISAM` 适合读操作多且数据不频繁更新的场景。
  
- **分区表**：将大表分成多个较小的物理块，加快查询速度。

---



# 三、案例

### **1. CTE 使用案例**

- **案例 1：CTE 筛选用户并匹配用户情况**
  
  使用CTE可以依次筛选出满足条件的用户并计算其试卷完成次数与题目练习次数。
  
  ```sql
  WITH hard_sql_users AS (
      SELECT ui.uid
      FROM (
          SELECT uid
          FROM exam_record
          WHERE exam_id IN (
              SELECT exam_id
              FROM examination_info
              WHERE difficulty = 'hard' AND tag = 'SQL'
          )
          GROUP BY uid
          HAVING AVG(score) > 80
      ) er
      JOIN user_info ui ON er.uid = ui.uid
      WHERE ui.level = 7
  ),
  exam_counts AS (
      SELECT uid, COUNT(*) AS exam_cnt
      FROM exam_record
      WHERE YEAR(submit_time) = 2021
      GROUP BY uid
  ),
  question_counts AS (
      SELECT uid, COUNT(*) AS question_cnt
      FROM practice_record
      WHERE YEAR(submit_time) = 2021
      GROUP BY uid
  )
  SELECT hsu.uid,
         COALESCE(ec.exam_cnt, 0) AS exam_cnt,
         COALESCE(qc.question_cnt, 0) AS question_cnt
  FROM hard_sql_users hsu
  JOIN exam_counts ec ON hsu.uid = ec.uid
  LEFT JOIN question_counts qc ON hsu.uid = qc.uid
  ORDER BY ec.exam_cnt ASC, qc.question_cnt DESC;
  ```

- **案例 2：统计多个条件下的活跃用户**

  使用CTE计算6/7级用户的活跃天数等指标。
  
  ```sql
  WITH 
      matched_users AS (
          SELECT uid
          FROM user_info
          WHERE level IN (6, 7)
      ),
      act_month_total AS (
          SELECT uid, COUNT(DISTINCT activity_month) AS act_month_total
          FROM (
              SELECT uid, CONCAT(YEAR(submit_time), '-', MONTH(submit_time)) AS activity_month
              FROM exam_record
              UNION
              SELECT uid, CONCAT(YEAR(submit_time), '-', MONTH(submit_time)) AS activity_month
              FROM practice_record
          ) AS all_activity_month
          GROUP BY uid
      ),
      act_days_2021_exam AS (
          SELECT uid, COUNT(DISTINCT DATE(submit_time)) AS act_days_2021_exam
          FROM exam_record
          WHERE YEAR(submit_time) = 2021
          GROUP BY uid
      ),
      act_days_2021_question AS (
          SELECT uid, COUNT(DISTINCT DATE(submit_time)) AS act_days_2021_question
          FROM practice_record
          WHERE YEAR(submit_time) = 2021
          GROUP BY uid
      ),
      total_act_days_2021 AS (
          SELECT uid, COUNT(DISTINCT activity_date) AS act_days_2021
          FROM (
              SELECT uid, DATE(submit_time) AS activity_date
              FROM exam_record
              WHERE YEAR(submit_time) = 2021
              UNION
              SELECT uid, DATE(submit_time) AS activity_date
              FROM practice_record
              WHERE YEAR(submit_time) = 2021
          ) AS all_activity_day2021
          GROUP BY uid
      )
  SELECT
      mu.uid,
      COALESCE(amt.act_month_total, 0) AS act_month_total,
      COALESCE(tad.act_days_2021, 0) AS act_days_2021,
      COALESCE(ade.act_days_2021_exam, 0) AS act_days_2021_exam,
      COALESCE(adq.act_days_2021_question, 0) AS act_days_2021_question
  FROM matched_users mu
  LEFT JOIN act_month_total amt ON mu.uid = amt.uid
  LEFT JOIN act_days_2021_exam ade ON mu.uid = ade.uid
  LEFT JOIN act_days_2021_question adq ON mu.uid = adq.uid
  LEFT JOIN total_act_days_2021 tad ON mu.uid = tad.uid
  ORDER BY act_month_total DESC, act_days_2021 DESC;
  ```

### **2. 条件聚合**

- **示例：统计用户在 2021 年的活动天数**
  
  ```sql
  COUNT(DISTINCT CASE WHEN YEAR(a.submit_time) = 2021 THEN DATE(a.submit_time) END) AS act_days_2021
  ```

### **3. 合并查询与左右连接**

- **JOIN 与 UNION 的使用**
  
  ```sql
  SELECT uid, exam_cnt + question_cnt AS total_cnt
  FROM (
      SELECT uid, COUNT(*) AS exam_cnt
      FROM exam_record
      WHERE YEAR(submit_time) = 2021
      GROUP BY uid
      UNION ALL
      SELECT uid, COUNT(*) AS question_cnt
      FROM practice_record
      WHERE YEAR(submit_time) = 2021
      GROUP BY uid
  ) AS activity_counts;
  ```

### **4. 日期处理**

- **提取年、月、日**
  
  ```sql
  SELECT 
      DATE_FORMAT(submit_time, '%Y-%m-%d') AS formatted_date
  FROM 
      exam_record;
  ```

### **5. 排名与窗口函数**

- **排序函数：**
  - `rank()`、`row_number()`、`dense_rank()` 用于数据的排序和排名。
  
  ```sql
  SELECT uid, 
         RANK() OVER (ORDER BY score DESC) AS rank
  FROM exam_record;
  ```

### **6. 文本处理**

- **正则表达式与分词**
  
  ```sql
  SELECT 
      REGEXP_SUBSTR(profile, '[^,]+$', 1, 1) AS last_segment
  FROM 
      user_info;
  ```

### **7. 子查询与聚合函数**

- **计算均值、总和等**
  
  ```sql
  SELECT AVG(score) AS average_score, SUM(score) AS total_score
  FROM exam_record
  WHERE exam_id IN (SELECT exam_id FROM examination_info WHERE difficulty = 'hard');
  ```

### **8. 示例的排序与分组**

  ```sql
  WITH exam_ranking AS (
      SELECT uid, exam_id, score,
             RANK() OVER (PARTITION BY exam_id ORDER BY score DESC) AS rank
      FROM exam_record
  )
  SELECT *
  FROM exam_ranking
  WHERE rank <= 3;
  ```

当然有！以下是几个SQL进阶技巧的应用案例，包括窗口函数、复杂的JOIN、子查询、文本处理和日期处理的组合使用：

### **9. 找出每个部门薪资最高的员工**

假设有一个员工表`employees`，包含`employee_id`、`department_id`和`salary`字段。目标是找到每个部门中薪资最高的员工。

```sql
SELECT department_id, employee_id, salary
FROM (
    SELECT department_id, employee_id, salary,
           ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rank
    FROM employees
) AS ranked_salaries
WHERE rank = 1;
```
**知识点**：窗口函数`ROW_NUMBER()`按部门分组并按薪水降序排序。最终通过`WHERE rank = 1`来获取每个部门中薪水最高的员工。

---

### **10. 计算每个产品的平均订单数量和总销售额**

假设有一个订单表`orders`，包含`order_id`、`product_id`、`quantity`和`price`字段。目标是统计每个产品的平均订单数量和总销售额。

```sql
SELECT product_id,
       AVG(quantity) AS avg_quantity,
       SUM(quantity * price) AS total_sales
FROM orders
GROUP BY product_id
HAVING AVG(quantity) > 5;  -- 只显示平均订单数量大于5的产品
```
**知识点**：使用`GROUP BY`结合`AVG`和`SUM`聚合函数，并通过`HAVING`子句筛选符合条件的分组。

---

### **11. 使用CTE进行递归查询：查找员工的所有下属**

假设有一个员工层级关系表`employees`，包含`employee_id`和`manager_id`字段，表示每个员工的直接上司。目标是查找某个特定员工的所有下属（直接和间接）。

```sql
WITH RECURSIVE employee_hierarchy AS (
    SELECT employee_id, manager_id
    FROM employees
    WHERE employee_id = 1  -- 指定起始员工ID
    UNION ALL
    SELECT e.employee_id, e.manager_id
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT employee_id
FROM employee_hierarchy;
```
**知识点**：递归CTE通过递归遍历获取指定员工的所有下属，适用于分层数据查询。

---

### **12. 使用正则表达式分割文本：获取邮箱域名**

假设有一个表`users`，包含用户的`user_id`和`email`。目标是提取邮箱的域名部分。

```sql
SELECT user_id,
       email,
       REGEXP_SUBSTR(email, '@(.+)$') AS domain
FROM users;
```
**知识点**：`REGEXP_SUBSTR`函数用于从邮箱地址中提取出域名部分。

---

### **13. 通过窗口函数计算日均订单数量**

假设有一个订单表`orders`，包含`order_date`和`order_id`。目标是按月份计算日均订单数量。

```sql
WITH daily_order_counts AS (
    SELECT DATE(order_date) AS order_date, COUNT(*) AS daily_orders
    FROM orders
    GROUP BY DATE(order_date)
)
SELECT DATE_FORMAT(order_date, '%Y-%m') AS order_month,
       AVG(daily_orders) AS avg_daily_orders
FROM daily_order_counts
GROUP BY DATE_FORMAT(order_date, '%Y-%m');
```
**知识点**：使用CTE计算每天的订单数，然后通过外层查询对其按月分组并计算日均订单数量。

---

### **14. 子查询+JOIN：获取客户购买过的所有商品类别**

假设有一个客户表`customers`，包含`customer_id`，以及一个订单明细表`order_details`，包含`order_id`、`customer_id`和`product_category`。目标是找到每个客户购买过的所有商品类别。

```sql
SELECT c.customer_id,
       GROUP_CONCAT(DISTINCT od.product_category ORDER BY od.product_category ASC) AS categories
FROM customers c
JOIN order_details od ON c.customer_id = od.customer_id
GROUP BY c.customer_id;
```
**知识点**：使用`GROUP_CONCAT`将每个客户购买的所有不同商品类别组合成一行结果。`ORDER BY`和`DISTINCT`在`GROUP_CONCAT`中确保结果按类别排序且无重复。

---

### **15. 计算每个月的新增用户数和活跃用户数**

假设有一个用户表`users`，包含`user_id`和`signup_date`，以及一个用户活动表`user_activities`，包含`user_id`和`activity_date`。目标是计算每个月的新增用户数和活跃用户数。

```sql
WITH monthly_signups AS (
    SELECT DATE_FORMAT(signup_date, '%Y-%m') AS signup_month, COUNT(*) AS new_users
    FROM users
    GROUP BY signup_month
),
monthly_active_users AS (
    SELECT DATE_FORMAT(activity_date, '%Y-%m') AS activity_month, COUNT(DISTINCT user_id) AS active_users
    FROM user_activities
    GROUP BY activity_month
)
SELECT ms.signup_month,
       COALESCE(ms.new_users, 0) AS new_users,
       COALESCE(mau.active_users, 0) AS active_users
FROM monthly_signups ms
LEFT JOIN monthly_active_users mau ON ms.signup_month = mau.activity_month
ORDER BY ms.signup_month;
```
**知识点**：通过两个CTE分别计算新增用户数和活跃用户数，并用`LEFT JOIN`进行合并。`COALESCE`函数处理没有活跃用户数据的月份，使其显示为0。

### 16. 嵌套子查询

**问题1 ：统计2021年每个未完成试卷作答数大于1的有效用户的数据（有效用户指完成试卷作答数至少为1且未完成数小于5），输出用户ID、未完成试卷作答数、完成试卷作答数、作答过的试卷tag集合，按未完成试卷数量由多到少排序**

**问题分解：**

- 关联作答记录和试卷信息：left join examination_info on using(exam_id)；（题中exam_record中的exam_id在examination_info均存在，所以用left join和inner join效果一样）

- 筛选2021年的记录：where year(start_time)=2021

- 获取各用户的tag,start_time及未完成标记和已完成标记，如果该作答记录交卷了则已完成标记为1，未完成标记为0，否则相反：if(submit_time is null, 1, null) as incomplete

- 按用户分组：group by uid

- 统计未完成试卷作答数和已完成试卷作答数：count(incomplete) as incomplete_cnt

- 统计作答过的tag集合：

  - 对于每条作答tag，用:连接日期和tag：`concat_ws(':', date(start_time), tag)`
  - 对于一个人（组内）的多条作答，用;连接去重后的作答记录：`group_concat(distinct concat_ws(':', date(start_time), tag) SEPARATOR ';')`

- 筛选未完成试卷作答数大于1的有效用户：

  ```sql
  having complete_cnt >= 1 and incomplete_cnt BETWEEN 2 and 4
  ```

  - 完成试卷作答数至少为1：complete_cnt >= 1
  - 未完成数小于5：incomplete_cnt < 5
  - 未完成试卷作答数大于1：incomplete_cnt > 1

```sql
  SELECT uid, 
    SUM(incomplete) AS incomplete_cnt,
    SUM(complete) AS complete_cnt,
    GROUP_CONCAT(DISTINCT CONCAT_WS(':', DATE(start_time), tag) ORDER BY start_time ASC SEPARATOR ';') AS detail
FROM (
    SELECT uid, tag, start_time,
           IF(submit_time IS NULL, 1, 0) AS incomplete,
           IF(submit_time IS NOT NULL, 1, 0) AS complete
    FROM exam_record 
    LEFT JOIN examination_info USING(exam_id)
    WHERE YEAR(start_time) = 2021
) AS exam_complete_rec
GROUP BY uid
HAVING complete_cnt >= 1 AND incomplete_cnt BETWEEN 2 AND 4
ORDER BY incomplete_cnt DESC;

```

**问题2：请从表中统计出 “当月均完成试卷数”不小于3的用户们爱作答的类别及作答次数，按次数降序输出，示例输出如下：**

问题拆解：

- 筛选完成了的试卷的记录。知识点：where
- 筛选月均完成数不小于3的用户。知识点：
  - 按用户分组group by uid；
  - 统计当前用户完成试卷总数count(exam_id)；
  - 统计该用户有完成试卷的月份数`count(distinct DATE_FORMAT(start_time, "%Y%m"))`；
  - 分组后过滤`having count(exam_id) / count(distinct DATE_FORMAT(start_time, "%Y%m")) >= 3`；
- 关联试卷作答记录表和试卷信息表。知识点：join examination_info using(exam_id)
- 筛选满足条件的用户。知识点：where uid in (...)
- 统计这些用户作答的类别及计数。知识点：按用户分组group by uid；计数count(tag)；
- 按次数降序输出。知识点：order by tag_cnt desc

```SQL
SELECT
    ei.tag,
    COUNT(er.start_time) AS tag_cnt
FROM
    exam_record er
LEFT JOIN
    examination_info ei
    ON ei.exam_id = er.exam_id
WHERE
    er.uid IN (
        -- 子查询：筛选出递交过3次或以上的用户
        SELECT uid
        FROM exam_record
        WHERE submit_time IS NOT NULL
        GROUP BY uid
        HAVING COUNT(submit_time)/COUNT(distinct DATE_FORMAT(start_time, "%Y%m")) >= 3
    ) -- 月均
GROUP BY 
    ei.tag
ORDER BY 
    tag_cnt DESC;

```

