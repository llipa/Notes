# MySQL

## 一、MySQL简介

### 1. 数据库相关概念

* **DB（Database）数据库：**是保存有组织的数据的容器。
* **DBMS（Database Management System）数据库软件：**是创建和管理数据库的工具。

### 2. 常见DBMS

#### 2.1 关系型数据库（RDBMS）

* Oracle
* MySQL
* SQL Server

#### 2.2 非关系型数据库

* 键值型数据库：Redis
* 文档型数据库：MongoDB、CouchDB
* 搜索引擎数据库：Solr、Elasticsearch、Splunk
* 列式数据库：HBase
* 图形数据库：Neo4J、InfoGrid

### 3. 主键与外键

* 数据库多以表的形式来存储数据：
  * 主键：一列或一组列，其值能唯一区分表中的每个行。
  * 外键：表中的某一列，包含另一个表的主键值，定义了两个表之间的关系。

### 4. MySQL图形化管理界面

* MySQL Workbench
* Navicat
* SQLyog
* dbeaver

### 5. SQL分类

* SQL在功能上主要分为3大类：
  * **DDL（Data Definition Languages，数据定义语言）**：定义不同的数据库、表、视图、索引等数据库对象，还可以用来创建、删除、修改数据库和数据表的结构。
    * 如`CREATE`、`DROP`、`ALTER`等。
  * **DML（Data Manipulation Language，数据操作语言）**：用于添加、删除、更新和查询数据库记录，并检查数据完整性。
    * 如`INSERT`、`DELETE`、`UPDATE`、`SELECT`等。
  * **DCL（Data Control Language，数据控制语言）**：用于定义数据库、表、字段、用户的访问权限和安全级别。
    * 如`GRANT`、`REVOKE`、`COMMIT`、`ROLLBACK`、`SAVEPOINT`等。

## 二、MySQL语言

* MySQL安装后会自动在后台运行。
* 在命令行输入`mysql -u root -p`，可以通过root账户连接到MySQL服务器。
* 输入`exit`退出MySQL命令行，此时MySQL服务器仍在后台运行。

> SQL语句不区分大小写，但为了便于阅读和调试，可将SQL关键字大写。

### 1. 执行`.sql`脚本

```mysql
# 在mysql中执行sql脚本
source xxx.sql
```

### 2. 查看数据库和表（SHOW）

```mysql
# 查看已有数据库
SHOW DATABASES;

# 查看所选数据库已有的表
SHOW TABLES;

# 查看表的详细信息
SHOW COLUMNS FROM customers;
DESCRIBE customers;
```

### 3. 选择数据库（USE）

```mysql
USE crashcourse;
```

### 4. 检索数据（SELECT）

```mysql
# 在表中检索某一列
SELECT name
FROM students;

# 检索多个列
SELECT name, gender, score
FROM students;

# 重命名score为points
SELECT name, gender, score points
FROM students;

# 检索所有列
SELECT *
FROM students;

# 检索某列中不同的值
SELECT DISTINCT class_id
FROM students;

# 检索某列前5行
SELECT name
FROM students
LIMIT 5;

# 检索某列第5行开始的5行, 需要注意的是行号是从0开始的
SELECT name
FROM students
LIMIT 5,5;
```

* 也可以使用完全限定的名字`test.students`、`students.name`、`test.students.name`等。
* `SELECT`语句不一定需要`FROM`，`FROM`只是`SELECT`语句的子句之一。可以使用`SELECT 1;`来检测数据库连接。

### 5. 排序检索数据（ORDER BY）

```mysql
# 根据某列的顺序(升序)排序检索
SELECT name
FROM students
ORDER BY score;

# 对多个列的顺序（升序）排序检索，列的优先级为指令中的出现顺序
SELECT name, score
FROM students
ORDER BY score, gender;

# 默认排序顺序为升序ASC，采用降序DESC关键字只作用于其前面的列名
SELECT name, score
FROM students
ORDER BY score DESC, gender;
```

* 使用`LIMIT`，必须位于`ORDER BY`之后。

### 6. 条件查询（WHERE）

```mysql
# 根据某列的值进行查询
SELECT name, score
FROM students
WHERE score > 90;

SELECT name, score
FROM students
WHERE score != 90;

SELECT name, score
FROM students
WHERE score <> 90;			# 不等于

SELECT name, score
FROM students
WHERE score BETWEEN 80 AND 90;

SELECT name, score
FROM students
WHERE score IS NULL;		# 空值

SELECT name, score
FROM students
WHERE score <=> NULL;		# 安全等于，可对NULL进行比较
```

* 同时使用`ORDER BY`和`WHERE`子句时，`ORDER BY`应位于`WHERE`之后。

```mysql
# AND操作符
SELECT name, score, gender
FROM students
WHERE score > 80 AND gender = 'M';

# OR操作符
SELECT name, score, gender
FROM students
WHERE score > 80 OR gender = 'M';
```

* 组合使用`AND`、`OR`操作符时，`AND`操作符优先级更高，因此应使用`()`来指定操作顺序。

```mysql
# IN操作符
SELECT name, class_id
FROM students
WHERE class_id IN (2, 3);

# NOT操作符
SELECT name, class_id
FROM students
WHERE class_id NOT IN (2, 3);
```

* MySQL中`NOT`支持对`IN`、`BETWEEN`、`EXISTS`子句取反。

### 7. 通配符搜索（LIKE）

* 通配符用于匹配查询时值的一部分信息。
* 使用通配符必须使用`LIKE`操作符。
* 通配符搜索处理一般比其他搜索处理要慢。

#### 7.1 `%`：任何多个字符

```mysql
# 例如'jet%'表示jet后面接任意字符
# '%jet%'表示包含jet的任意字符
# 's%e'表示s开头e结尾的任意字符
SELECT name, score FROM students WHERE score LIKE '%9%';
```

#### 7.2 `_`：任意单个字符

```mysql
# 和%用法一样，但是仅能匹配单个字符
SELECT name, score FROM students WHERE score LIKE '9_';
```

### 8. 正则表达式搜索

* 通配符可以用于基本的过滤，但对于负责过滤条件，则需要正则表达式来匹配文本。

#### 8.1 `REGEXP`：后接的所有东西均为正则表达式

* `REGEXP`对列内的值进行匹配，`LIKE`对列进行匹配。

#### 8.2 `.`：匹配任意一个字符

```mysql
SELECT name, score FROM students WHERE score REGEXP '9.';
```

#### 8.3 `|`和`[]`：或匹配和单字符或匹配

```mysql
# 或匹配
SELECT name, score FROM students WHERE score REGEXP '81|85';
# 单字符或匹配，匹配81或85
SELECT name, score FROM students WHERE score REGEXP '8[15]';
# 范围匹配，匹配81-85
SELECT name, score FROM students WHERE score REGEXP '8[1-5]';
```

#### 8.4 `?`：匹配单个字符的出现与否

```mysql
# ?表示其前面的字符可有可无，即s可有可无
SELECT prod_name FROM products WHERE prod_name REGEXP '\\([0-9] sticks?\\)';
```

#### 8.5 `{}`：指定数目的匹配

```mysql
# 搜索任意连续的四个数字
SELECT prod_name FROM products WHERE prod_name REGEXP '[[:digit:]]{4}';
```

#### 8.6 `\\`：匹配特殊字符

* 具有特殊含义的字符无法直接搜索，因此需要`\\.`表示`.`，其他特殊字符同理。

#### 8.7 定位符

* 定位符指定了应匹配文本的位置。
* **`^`：文本的开始。**
* **`$`：文本的结尾。**
* **`[[:<:]]`：词的开始。**
* **`[[:>:]]`：词的结尾。**

```mysql
# 例子：搜索开头为数字或.
SELECT prod_name FROM products WHERE prod_name REGEXP '^[[:digit:]\\.]';
```

#### 8.8 匹配字符类

* 预定义常用字符集，称为字符类。
* `[:alnum:]`：任意字母和数字。
* `[:alpha:]`：任意字母。
* `[:blank:]`：空格和制表。
* `[:cntrl:]`：ASCII控制字符。
* `[:digit:]`：任意数字。
* `[:print:]`：任意可打印字符。
* `[:lower:]`：任意小写字母。
* `[:upper:]`：任意大写字母。

### 9. 计算字段

* 计算字段是运行时在`SELECT`语句内创建的，并不存在于数据库中。

> 虽然SQL语句内完成的许多转换和格式化工作都能在客户机上完成，但在服务器上完成会更快。

#### 拼接字段

```mysql
# Concat()将多个列的值合并后再输出
SELECT Concat(name, ' score: ', score) FROM students ORDER BY score;
# 使用别名替换Concat(...) # 也可以像前面那样采用' '来重命名
SELECT Concat(name, ' score: ', score) AS final_test FROM students ORDER BY score;
```

#### 算术操作

```mysql
# 可以对列中的值进行加减乘除操作
SELECT Concat(name, ' score: ', score*2) AS final_test FROM students ORDER BY score;
```

### 10. 聚合查询

* **聚集函数**：运行在行组上，计算和返回单个值的函数。

```mysql
# AVG()		返回某列的平均值
# COUNT()	返回某列的行数
# MAX()		返回某列的最大值
# MIN()		返回某列的最小值
# SUM()		返回某列的和
SELECT AVG(score) AS avg_score FROM students;
# DISTINCT参数用于对列中不同值进行聚合查询
SELECT AVG(DISTINCT score) AS avg_score FROM students;
```

### 11. 分组数据（GROUP BY / HAVING）

```mysql
# 创建分组，对所有学生按照性别进行统计
SELECT gender, COUNT(*) AS num_stu FROM students GROUP BY gender;
# 过滤分组，WHERE过滤行，HAVING过滤分组
SELECT score, COUNT(*) AS num_stu FROM students GROUP BY score HAVING COUNT(*) > 1;
```

> `WHERE`在分组前进行过滤，`HAVING`在分组后进行过滤。

### 12. 子查询

* 子查询返回的值用于上一层查询。

```mysql
# 子查询返回1，因此就是筛选了class_id为1的行
SELECT score, name FROM students WHERE class_id IN (SELECT id FROM classes WHERE name = '一班');
```

* 填充计算列：将检索出的值用于子查询。

```mysql
# 对每一行都执行一次子查询，即为从classes表中取出相应class_id的班级名称
SELECT score, name, (SELECT name FROM classes WHERE id = students.class_id) AS class FROM students ORDER BY score DESC;
```

### 13. 联结（JOIN ON）

* 为了进行多表查询，需要对表进行关联，称为联结。

#### 13.1 等值联结

```mysql
# 等值联结是内部联结，INNER可以省略
SELECT classes.name, students.name, score
FROM students INNER JOIN classes
ON classes.id = students.class_id;

# 其实该查询语句等价于：
SELECT classes.name, students.name, score
FROM students, classes
WHERE classes.id = students.class_id;
```

* **非等值联结**即为联结条件不为=的联结。

#### 13.2 自联结

```mysql
# 在同一张表products中筛选出prod_id为DTNTR的vend_id，并找出该表中所有vend_id为这个值的内容
SELECT p1.prod_id, p1.prod_name
FROM products p1, products p2
WHERE p1.vend_id = p2.vend_id AND p2.prod_id = 'DTNTR';
```

#### 13.3 外部联结（LEFT / RIGHT OUTER JOIN ON）

```mysql
# 外部联结包含了在相关表中没有关联行的行
# LEFT和RIGHT分别表示指定OUTER JOIN左/右边的表为所有行的基准
SELECT customers.cust_id, orders.order_num
FROM customers LEFT OUTER JOIN orders
ON customers.cust_id = orders.cust_id;
```

* `Oracle`还支持`FULL JOIN OUT`满外联结，对左右表没有关联的行也筛选。

#### 13.4 七种`JOIN`的实现

![7种JOIN操作](pictures/join.jpg)

* `JOIN`分七种操作。

```mysql
# 中图：内联结
SELECT employee_id, department_name
FROM employees e JOIN departments d
ON e.department_id = d.department_id;

# 左上：左外联结
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id = d.department_id;

# 右上：右外联结
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id = d.department_id;

# 左中
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id = d.department_id
WHERE d.department_id IS NULL;

# 右中
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id = d.department_id
WHERE e.department_id IS NULL;

# 左下：满外联结
# exp：左上+右中
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id = d.department_id
UNION ALL
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id = d.department_id
WHERE e.department_id IS NULL;

# 右下
# exp：左中+右中
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id = d.department_id
WHERE d.department_id IS NULL
UNION ALL
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id = d.department_id
WHERE e.department_id IS NULL;
```



### 14. 组合查询（UNION / UNION ALL）

* 组合查询是将多个查询的结果作为单个查询结果返回。
* `UNION`返回交集，`UNION ALL`返回两集合相加。
  * `UNION ALL`效率更高，尽量使用`UNION ALL`。

```mysql
# UNION中每个查询必须包含相同的列、表达式或聚集函数
# 列的数据类型必须兼容（可以互相转换）
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <= 5
UNION
SELECT vend_id, prod_id, prod_price
FROM products
WHERE vend_id IN (1001, 1002);
```

> * UNION的多个查询语句中仅能有一个`ORDER BY`。

### 15.  全文本搜索

> 并非所有引擎都支持全文本搜索。MyISAM支持而InnoDB不支持。

* 使用`Match()`和`Against()`函数进行全文本搜索。

