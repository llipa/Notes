# MySQL

### MySQL简介

* **数据库**是保存有组织的数据的容器。**数据库软件（DBMS）**是创建和管理数据库的工具。
* 数据库多以表的形式来存储数据：
  * 主键：一列或一组列，其值能唯一区分表中的每个行。
  * 外键：表中的某一列，包含另一个表的主键值，定义了两个表之间的关系。

* DBMS分两类：
  * 基于共享文件系统的DBMS。
  * 基于客户机-服务器的DBMS（例如MySQL）。
* 每个MySQL安装都有一个名为mysql的简单命令行实用程序。这是用于跟MySQL交互的客户机。
  * MySQL Administrator和MySQL Query Brower是图形交互客户机。

### 使用MySQL

* MySQL安装后会自动在后台运行。
* 在命令行输入`mysql -u root -p`，可以通过root账户连接到MySQL服务器。
* 输入`exit`退出MySQL命令行，此时MySQL服务器仍在后台运行。

> SQL语句不区分大小写，但为了便于阅读和调试，可将SQL关键字大写。

#### sql脚本

```mysql
# 在mysql中执行sql脚本
source xxx.sql
```

#### 查看数据库和表（SHOW）

```mysql
# 查看已有数据库
SHOW DATABASES;
# 查看所选数据库已有的表
SHOW TABLES;
# 查看表的详细信息
SHOW COLUMNS FROM customers;
DESCRIBE customers;
```

#### 选择数据库（USE）

```mysql
USE crashcourse;
```

#### 检索数据（SELECT）

```mysql
# 在表中检索某一列
SELECT name FROM students;
# 检索多个列
SELECT name, gender, score FROM students;
# 重命名score为points
SELECT name, gender, score points FROM students;
# 检索所有列
SELECT * FROM students;
# 检索某列中不同的值
SELECT DISTINCT class_id from students;
# 检索某列前5行
SELECT name FROM students LIMIT 5;
# 检索某列第5行开始的5行, 需要注意的是行号是从0开始的
SELECT name FROM students LIMIT 5,5;
```

* 也可以使用完全限定的名字`test.students`、`students.name`、`test.students.name`等。
* `SELECT`语句不一定需要`FROM`，`FROM`只是`SELECT`语句的子句之一。可以使用`SELECT 1;`来检测数据库连接。

#### 排序检索数据（ORDER BY）

```mysql
# 根据某列的顺序(升序)排序检索
SELECT name FROM students ORDER BY score;
# 对多个列的顺序（升序）排序检索，列的优先级为指令中的出现顺序
SELECT name, score FROM students ORDER BY score, gender;
# 默认排序顺序为升序ASC，采用降序DESC关键字只作用于其前面的列名
SELECT name, score FROM students ORDER BY score DESC, gender;
```

* 使用`LIMIT`，必须位于`ORDER BY`之后。

#### 条件查询（WHERE）

```mysql
# 根据某列的值进行查询
SELECT name, score FROM students WHERE score > 90;
SELECT name, score FROM students WHERE score != 90;
SELECT name, score FROM students WHERE score <> 90;			# 不等于
SELECT name, score FROM students WHERE score BETWEEN 80 AND 90;
SELECT name, score FROM students WHERE score IS NULL;		# 空值
```

* 同时使用`ORDER BY`和`WHERE`子句时，`ORDER BY`应位于`WHERE`之后。

```mysql
# AND操作符
SELECT name, score, gender FROM students WHERE score > 80 AND gender = 'M';
# OR操作符
SELECT name, score, gender FROM students WHERE score > 80 OR gender = 'M';
```

* 组合使用`AND`、`OR`操作符时，`AND`操作符优先级更高，因此应使用`()`来指定操作顺序。

```mysql
# IN操作符
SELECT name, class_id FROM students WHERE class_id IN (2, 3);
# NOT操作符
SELECT name, class_id FROM students WHERE class_id NOT IN (2, 3);
```

* MySQL中`NOT`支持对`IN`、`BETWEEN`、`EXISTS`子句取反。

#### 通配符搜索（LIKE）

* 通配符用于匹配查询时值的一部分信息。
* 使用通配符必须使用`LIKE`操作符。
* 通配符搜索处理一般比其他搜索处理要慢。

##### `%`：任何多个字符

```mysql
# 例如'jet%'表示jet后面接任意字符
# '%jet%'表示包含jet的任意字符
# 's%e'表示s开头e结尾的任意字符
SELECT name, score FROM students WHERE score LIKE '%9%';
```

##### `_`：任意单个字符

```mysql
# 和%用法一样，但是仅能匹配单个字符
SELECT name, score FROM students WHERE score LIKE '9_';
```

#### 正则表达式搜索

* 通配符可以用于基本的过滤，但对于负责过滤条件，则需要正则表达式来匹配文本。

##### `REGEXP`：后接的所有东西均为正则表达式

* `REGEXP`对列内的值进行匹配，`LIKE`对列进行匹配。

##### `.`：匹配任意一个字符

```mysql
SELECT name, score FROM students WHERE score REGEXP '9.';
```

##### `|`和`[]`：或匹配和单字符或匹配

```mysql
# 或匹配
SELECT name, score FROM students WHERE score REGEXP '81|85';
# 单字符或匹配，匹配81或85
SELECT name, score FROM students WHERE score REGEXP '8[15]';
# 范围匹配，匹配81-85
SELECT name, score FROM students WHERE score REGEXP '8[1-5]';
```

##### `?`：匹配单个字符的出现与否

```mysql
# ?表示其前面的字符可有可无，即s可有可无
SELECT prod_name FROM products WHERE prod_name REGEXP '\\([0-9] sticks?\\)';
```

##### `{}`：指定数目的匹配

```mysql
# 搜索任意连续的四个数字
SELECT prod_name FROM products WHERE prod_name REGEXP '[[:digit:]]{4}';
```

##### `\\`：匹配特殊字符

* 具有特殊含义的字符无法直接搜索，因此需要`\\.`表示`.`，其他特殊字符同理。

##### 定位符

* 定位符指定了应匹配文本的位置。
* **`^`：文本的开始。**
* **`$`：文本的结尾。**
* **`[[:<:]]`：词的开始。**
* **`[[:>:]]`：词的结尾。**

```mysql
# 例子：搜索开头为数字或.
SELECT prod_name FROM products WHERE prod_name REGEXP '^[[:digit:]\\.]';
```

##### 匹配字符类

* 预定义常用字符集，称为字符类。
* `[:alnum:]`：任意字母和数字。
* `[:alpha:]`：任意字母。
* `[:blank:]`：空格和制表。
* `[:cntrl:]`：ASCII控制字符。
* `[:digit:]`：任意数字。
* `[:print:]`：任意可打印字符。
* `[:lower:]`：任意小写字母。
* `[:upper:]`：任意大写字母。

#### 计算字段

* 计算字段是运行时在`SELECT`语句内创建的，并不存在于数据库中。

> 虽然SQL语句内完成的许多转换和格式化工作都能在客户机上完成，但在服务器上完成会更快。

##### 拼接字段

```mysql
# Concat()将多个列的值合并后再输出
SELECT Concat(name, ' score: ', score) FROM students ORDER BY score;
# 使用别名替换Concat(...) # 也可以像前面那样采用' '来重命名
SELECT Concat(name, ' score: ', score) AS final_test FROM students ORDER BY score;
```

##### 算术操作

```mysql
# 可以对列中的值进行加减乘除操作
SELECT Concat(name, ' score: ', score*2) AS final_test FROM students ORDER BY score;
```

#### 聚合查询

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

#### 分组数据（GROUP BY / HAVING）

```mysql
# 创建分组，对所有学生按照性别进行统计
SELECT gender, COUNT(*) AS num_stu FROM students GROUP BY gender;
# 过滤分组，WHERE过滤行，HAVING过滤分组
SELECT score, COUNT(*) AS num_stu FROM students GROUP BY score HAVING COUNT(*) > 1;
```

> `WHERE`在分组前进行过滤，`HAVING`在分组后进行过滤。

#### 子查询

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

#### 联结表

##### 等值联结（INNER JOIN ON）

```mysql
# 等值联结也称内部联结
SELECT classes.name, students.name, score FROM students INNER JOIN classes ON classes.id = students.class_id;
# 其实该查询语句等价于：
SELECT classes.name, students.name, score FROM students, classes WHERE classes.id = students.class_id;
```

##### 自联结

```mysql
# 在同一张表products中筛选出prod_id为DTNTR的vend_id，并找出该表中所有vend_id为这个值的内容
SELECT p1.prod_id, p1.prod_name FROM products AS p1, products AS p2 WHERE p1.vend_id = p2.vend_id AND p2.prod_id = 'DTNTR';
```

##### 外部联结（LEFT/RIGHT OUTER JOIN ON）

```mysql
# 外部联结包含了在相关表中没有关联行的行
# LEFT和RIGHT分别表示指定OUTER JOIN左/右边的表为所有行的基准
SELECT customers.cust_id, orders.order_num from customers LEFT OUTER JOIN orders ON customers.cust_id = orders.cust_id;
```

#### 组合查询（UNION）

* 组合查询是将多个查询的结果作为单个查询结果返回。

```mysql
# UNION中每个查询必须包含相同的列、表达式或聚集函数
# 列的数据类型必须兼容（可以互相转换）
SELECT vend_id, prod_id, prod_price FROM products WHERE prod_price <= 5 UNION SELECT vend_id, prod_id, prod_price FROM products WHERE vend_id IN (1001, 1002);
```

> * UNION会自动去掉重复的行。若要显示所有行，可以使用`UNION ALL`。
>
> * UNION的多个查询语句中仅能有一个`ORDER BY`。

#### 全文本搜索

> 并非所有引擎都支持全文本搜索。MyISAM支持而InnoDB不支持。

* 使用`Match()`和`Against()`函数进行全文本搜索。

