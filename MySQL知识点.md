#MySQL学习笔记

[TOC]

### 第一章 了解SQL ###

#### 数据库基础核心概念 ####

- **数据库定义**：存储有组织数据的容器，通常由文件组成，用户通过DBMS间接访问。
- **表结构**：特定类型数据的结构化清单，由列（字段）和行（记录）组成，每个表有唯一名称。
- **列与数据类型**：列存储表中部分信息，数据类型（如INT、VARCHAR）约束存储内容，确保数据完整性。易错点：错误数据类型可能导致排序或存储问题，如用VARCHAR存数值会影响计算效率。
- **行与记录**：表中每行代表一条记录，行数即记录总数。注意：术语“行”和“记录”常互换，但“行”更准确。
- **主键关键规则**：一列或多列，值唯一标识每行，不允许NULL值。最好实践：不更新主键、不重用值、避免使用可变值（如名字）。外键在第15章介绍。

#### SQL语言概述 ####

- **SQL目的**：与数据库通信的标准化语言，优点包括跨DBMS支持、简单易学、功能强大。
- **易错点**：SQL语法不区分大小写，但标识符（如数据库名）在MySQL 4.1前区分大小写；语句空格被忽略，可多行书写，但需保持一致性。

#### 关键字与子句对比 ####

| 概念    | 定义             | 示例/注意点                                    |
| ------- | ---------------- | ---------------------------------------------- |
| 数据库  | 数据存储容器     | 如`crashcourse`数据库，通过DBMS管理            |
| 表      | 特定类型数据清单 | `products`表存储产品信息，避免混合数据类型     |
| 主键    | 唯一标识行的列   | 条件：值唯一、非NULL；多列主键时所有列组合唯一 |
| SQL语句 | 数据库操作指令   | 以分号结束；子句如WHERE用于过滤                |

------

### 第二章 MySQL简介 ###

#### MySQL核心特性 ####

- **MySQL作为DBMS**：开源、高性能的关系数据库系统，支持两种主要DBMS架构： **基于共享文件系统的DBMS**（如Microsoft Access、FileMaker）：数据存储在本地文件中，适合单用户或小型桌面应用，性能有限 **基于客户机-服务器的DBMS**（如MySQL、Oracle）：服务器处理数据访问和存储，客户机提供用户界面，支持多用户并发访问
- **架构优势**：客户机-服务器模型提供更好的性能、安全性和可扩展性，MySQL采用此架构

#### MySQL工具详解 ####

- **mysql命令行实用程序**：核心文本界面工具，适合快速执行命令和脚本
- **MySQL Administrator**：图形化管理和监控工具，提供服务器状态、用户管理等功能
- **MySQL Query Browser**：可视化查询工具，支持多标签和语法高亮
- **MySQL Workbench**（新增）： **统一开发环境**：集成数据建模、SQL开发、服务器管理和迁移工具 **可视化设计**：支持ER图设计、正向/反向工程，可生成物理数据模型 **SQL开发**：提供智能代码补全、语法高亮和查询结果可视化 **管理功能**：服务器配置、用户权限管理、备份恢复操作 **跨平台支持**：适用于Windows、Linux和macOS系统

#### 工具对比表格 ####

| 工具                | 主要用途         | 核心功能                | 适用场景               |
| ------------------- | ---------------- | ----------------------- | ---------------------- |
| mysql命令行         | 基础操作、批处理 | 命令执行、脚本运行      | 服务器维护、自动化任务 |
| MySQL Administrator | 系统管理         | 用户管理、服务器监控    | 数据库管理员日常维护   |
| MySQL Query Browser | 查询开发         | 可视化查询、结果分析    | 开发人员SQL调试        |
| MySQL Workbench     | 综合开发环境     | 数据建模、SQL开发、管理 | 全生命周期数据库开发   |

#### 架构对比说明 ####

| DBMS类型      | 数据存储方式   | 典型产品                  | 适用场景               |
| ------------- | -------------- | ------------------------- | ---------------------- |
| 基于共享文件  | 本地文件存储   | Access、FileMaker         | 单机应用、小型项目     |
| 客户机-服务器 | 专用服务器存储 | MySQL、Oracle、SQL Server | 企业级应用、高并发场景 |

#### 注意事项 ####

- **连接问题**：若本地MySQL未运行，使用`mysql -u root -p`可能失败，需先启动服务。工具集成：在Query Browser中，通过菜单启动命令行工具，实现无缝切换。

- **版本兼容**：例如，MySQL 4.x不支持`LIMIT 5 OFFSET 3`语法，必须用`LIMIT 3,4`，否则报错。

- **客户机-服务器实践**：在生产环境中，MySQL服务器部署在专用机器上，开发人员通过Workbench远程连接，实现团队协作开发
- **Workbench数据建模**：使用Workbench的ER图工具设计数据库关系，自动生成DDL语句，确保模型与代码同步
- **架构选择考量**：对于需要高并发处理的电商系统，必须选择客户机-服务器架构；而个人博客可能使用基于文件的SQLite即可满足需求

------

### 第三章 使用MySQL ###

#### 数据库连接与选择 ####

- **连接要素**：主机名（本地用`localhost`）、端口（默认3306）、用户名、口令。易错点：网络问题或错误凭证导致拒绝连接；使用完全限定名如`database.table`避免歧义。
- **USE语句**：选择数据库，如`USE crashcourse;`，无输出但后续操作针对该库。注意：未先USE直接查询会报“无数据库选择”错误。

#### 探索数据库元数据 ####

- **SHOW命令家族**：检索数据库、表、列信息。例如，`SHOW DATABASES;`列表所有库；`SHOW COLUMNS FROM customers;`显示表结构，包括类型、键等。
- **信息模式与DESCRIBE**：MySQL 5+支持`INFORMATION_SCHEMA`，但`DESCRIBE table`是`SHOW COLUMNS`的快捷方式。易错点：SHOW命令需权限，无权用户可能看不到系统库。

#### SHOW命令对比表格 ####

| 命令                       | 功能           | 输出示例                             | 注意点                 |
| -------------------------- | -------------- | ------------------------------------ | ---------------------- |
| `SHOW DATABASES;`          | 列出所有数据库 | `information_schema", "crashcourse"` | 包含系统库，勿随意修改 |
| `SHOW TABLES;`             | 当前库中所有表 | `customers", "orders"`               | 需先USE数据库          |
| `SHOW COLUMNS FROM table;` | 表列详情       | 显示类型、NULL允许、键信息           | 等同`DESCRIBE table;`  |
| `SHOW STATUS;`             | 服务器状态     | 广泛性能指标                         | 输出复杂，需过滤解读   |

#### 注意事项 ####

- 元数据查询：假设需检查`orders`表的外键，可用`SHOW CREATE TABLE orders;`输出建表语句，解析约束细节，避免直接操作内部表。
- 错误处理：若`SHOW COLUMNS`返回空，可能表不存在或拼写错误；权限不足时输出受限，需用管理员账户。

---

### 第四章 检索数据 ###

#### SELECT语句核心结构 ####

- **基本语法**：`SELECT 列名 FROM 表名`，必须指定要检索的列或使用通配符*
- **列选择方式**： 单列检索：`SELECT prod_name FROM products` 多列检索：用逗号分隔，`SELECT prod_id, prod_name, prod_price` 所有列：`SELECT *`（生产环境慎用）
- **完全限定名**：使用`数据库名.表名.列名`避免歧义

#### DISTINCT关键字 ####

- **去重功能**：去除SELECT结果中的重复行，只对选定的==所有==列组合有效
- **语法位置**：必须紧接SELECT之后，如`SELECT DISTINCT vend_id`
- **NULL值处理**：DISTINCT将多个NULL视为相同值，只返回一个NULL
- **易错点**：==`SELECT DISTINCT vend_id, prod_price`是对两列组合去重，非单列==

#### LIMIT子句 ####

- **限制行数**：控制返回结果的数量，提高查询效率
- **两种语法形式**： `LIMIT 行数`：返回前n行 `LIMIT 开始位置, 行数`：从指定位置返回n行（开始位置从0计）
- **分页应用**：结合ORDER BY实现数据分页显示

#### 关键字功能对比表 ####

| 关键字   | 功能描述     | 语法示例                 | 适用场景        |
| -------- | ------------ | ------------------------ | --------------- |
| SELECT   | 指定检索列   | `SELECT col1, col2`      | 基础数据检索    |
| DISTINCT | 结果去重     | `SELECT DISTINCT col`    | 统计唯一值      |
| LIMIT    | 限制返回行数 | `LIMIT 10`或`LIMIT 5,10` | 分页、TOP N查询 |

#### 复杂查询示例 ####

```mysql
-- 多列去重结合排序和限制
SELECT DISTINCT vend_id, prod_price 
FROM products 
WHERE prod_price > 10 
ORDER BY vend_id DESC, prod_price 
LIMIT 2, 5;

-- 完全限定名在多数据库环境的应用
SELECT crashcourse.products.prod_name, 
       otherdb.suppliers.supp_name 
FROM crashcourse.products, otherdb.suppliers 
WHERE crashcourse.products.vend_id = otherdb.suppliers.vend_id;
```

------

### 第五章 排序检索数据 ###

#### ORDER BY子句详解 ####

- **基本排序**：`ORDER BY 列名`，默认升序(ASC)
- **多列排序**：按列顺序优先级，`ORDER BY col1, col2, col3`
- **指定方向**：每列可单独指定ASC(升序)或DESC(降序)
- **位置引用**：可用SELECT中的列位置代替列名，如`ORDER BY 2, 3`

#### 排序方向与优先级 ####

- **方向作用范围**：DESC/ASC只作用于紧邻的前一列
- **混合方向排序**：`ORDER BY col1 DESC, col2 ASC`
- **表达式排序**：可按计算字段或函数结果排序
- **易错点**：文本排序受字符集影响，数字以字符串形式排序会产生意外结果

#### 排序选项对比表 ####

| 排序方式 | 语法示例                       | 特点         | 注意事项             |
| -------- | ------------------------------ | ------------ | -------------------- |
| 单列排序 | `ORDER BY prod_name`           | 简单直接     | 默认升序             |
| 多列排序 | `ORDER BY vend_id, prod_price` | 层次化排序   | 列顺序决定优先级     |
| 指定方向 | `ORDER BY prod_price DESC`     | 控制排序方向 | 需逐列指定           |
| 位置排序 | `ORDER BY 2, 3`                | 简化书写     | 数字对应SELECT列位置 |

#### 复杂排序示例 ####

```mysql
-- 多列混合方向排序结合表达式
SELECT prod_name, prod_price, inventory_qty,
       prod_price * inventory_qty AS total_value
FROM products 
ORDER BY vend_id DESC, 
         total_value ASC, 
         prod_name DESC 
LIMIT 10;

-- 使用函数结果排序
SELECT cust_name, 
       LENGTH(cust_name) AS name_length,
       cust_country
FROM customers 
ORDER BY name_length DESC, cust_country ASC;
```

------

### 第六章 过滤数据 ###

#### WHERE子句基础操作符 ####

- **比较操作符**：=, <>, !=, <, <=, >, >=
- **范围操作符**：BETWEEN...AND...（闭区间包含边界值）
- **空值判断**：IS NULL, IS NOT NULL
- **易错点**：NULL值比较永远返回未知，需用IS NULL判断

#### 组合条件过滤 ####

- **AND操作符**：所有条件必须同时满足，优先级高于OR
- **OR操作符**：任一条件满足即可
- **括号优先级**：用括号明确计算顺序，`WHERE (A OR B) AND C`
- **IN操作符**：指定值列表，`WHERE col IN (值1, 值2)`

#### 操作符对比表 ####

| 操作符  | 功能描述      | 示例                   | NULL值处理           |
| ------- | ------------- | ---------------------- | -------------------- |
| =, <>   | 相等/不等比较 | `col = 10`             | 与NULL比较返回NULL   |
| BETWEEN | 范围匹配      | `col BETWEEN 5 AND 10` | 边界值包含           |
| IS NULL | 空值检查      | `col IS NULL`          | 专用于NULL检测       |
| AND     | 逻辑与        | `A AND B`              | 任一为NULL则结果未知 |
| OR      | 逻辑或        | `A OR B`               | 优先级低于AND        |

#### 复杂过滤示例 ####

```mysql
-- 多层条件组合与括号使用
SELECT prod_name, prod_price, vend_id
FROM products
WHERE (vend_id = 1003 OR vend_id = 1005)
  AND (prod_price BETWEEN 5 AND 20 
       OR prod_price IS NULL)
  AND prod_name LIKE '%anvil%';

-- IN与子查询结合
SELECT cust_name, cust_country
FROM customers
WHERE cust_country IN ('USA', 'UK')
  AND cust_id NOT IN (
    SELECT cust_id 
    FROM orders 
    WHERE order_date > '2023-01-01'
  );
```

------

### 第七章 数据过滤 ###

#### 高级条件组合 ####

- **NOT操作符**：否定后续条件，`WHERE NOT col > 10`
- **IN优势**：比多个OR执行效率高，可包含子查询
- **括号的重要性**：复杂逻辑中必须用括号明确优先级

#### 子查询在过滤中的应用 ####

- **子查询作为条件**：`WHERE col IN (SELECT ...)`
- **相关子查询**：子查询引用外部查询的列
- **EXISTS操作符**：检查子查询是否返回结果
- **易错点**：NOT IN遇到NULL值可能返回空结果集

#### 高级操作符对比表 ####

| 操作符      | 功能描述   | 语法示例                    | 性能考虑                 |
| ----------- | ---------- | --------------------------- | ------------------------ |
| NOT         | 逻辑否定   | `WHERE NOT EXISTS(...)`     | 可能影响索引使用         |
| IN + 子查询 | 动态条件   | `WHERE col IN (SELECT...)`  | 子查询结果集大小影响性能 |
| EXISTS      | 存在性检查 | `WHERE EXISTS(SELECT 1...)` | 通常比IN效率高           |

#### 复杂条件构建示例 ####

```mysql
-- 多层嵌套子查询与NOT组合
SELECT prod_name, vend_id
FROM products
WHERE vend_id IN (
  SELECT vend_id 
  FROM vendors 
  WHERE vend_country = 'USA'
    AND vend_id NOT IN (
      SELECT vend_id 
      FROM products 
      WHERE prod_price < 5
    )
)
AND prod_price > 10;

-- 使用EXISTS进行存在性检查
SELECT cust_name
FROM customers c
WHERE EXISTS (
  SELECT 1 
  FROM orders o 
  WHERE o.cust_id = c.cust_id 
    AND o.order_date > '2023-01-01'
)
AND cust_country = 'USA';
```

#### 注意事项

1. **关键字EXISTS**

   1. **核心作用**：它是一个**条件测试**，用在 `WHERE`子句中。它不关心子查询返回的具体数据，只关心**是否有结果返回**。
   2. **工作方式**：`EXISTS`对子查询运行一次。如果子查询返回**一行或多行**结果，则 `EXISTS`的结果为 `TRUE`，外部查询便会处理当前行。如果子查询没有返回任何行，结果为 `FALSE`，外部查询则忽略当前行。
   3. **典型用法**：通常是一个**关联子查询**，即子查询的查询条件依赖于外部查询的当前行。

   **示例**：找出下过订单的所有客户。

   ```MySQL
   SELECT cust_name FROM customers
   WHERE EXISTS (
     SELECT 1 FROM orders
     WHERE orders.cust_id = customers.cust_id
   );
   ```

   在这个例子中，对于 `customers`表的每一个客户，子查询都会检查 `orders`表中是否存在与该客户ID匹配的订单。只要存在至少一个订单，`EXISTS`就返回真，该客户信息就会被选出。

   **与 IN 的区别**：`EXISTS`更高效，因为它一旦在子查询中找到匹配就会停止搜索；而 `IN`会先获取子查询的所有结果集再进行匹配。

------

### 第八章 用通配符进行过滤 ###

#### LIKE操作符与通配符类型 ####

- **百分号%**：匹配任意字符出现任意次数（包括0次）
- **下划线_**：匹配单个任意字符
- **字符集范围**：`[charlist]`匹配指定字符集中的单个字符
- **排除字符集**：`[^charlist]`匹配不在指定字符集中的单个字符

#### 通配符使用技巧与性能 ####

- **模式构造**：通配符可置于模式开头、中间或结尾
- **转义特殊字符**：使用`\`转义通配符本身，如`LIKE '50%'`
- **性能影响**：前导通配符（如`%abc`）无法使用索引，导致全表扫描
- **优化策略**：尽量避免前导通配符，考虑全文搜索替代方案

#### 通配符对比表 ####

| 通配符   | 功能描述     | 示例匹配结果                    | 索引使用             |
| -------- | ------------ | ------------------------------- | -------------------- |
| %        | 任意长度匹配 | `'jet%'`匹配"jet", "jetpack"    | 后缀通配符可使用索引 |
| _        | 单字符匹配   | `'_ ton anvil'`匹配"1 ton"      | 特定情况下可使用索引 |
| 组合使用 | 复杂模式     | `'__ inch%'`匹配"12 inch anvil" | 通常无法使用索引     |

#### 复杂通配符示例 ####

```mysql
-- 多通配符组合与转义使用
SELECT prod_name, prod_desc
FROM products
WHERE (prod_name LIKE 'jetpack 1__' 
       OR prod_name LIKE 'jetpack 2__')
  AND prod_desc LIKE '%heavy-duty%'
  AND prod_name NOT LIKE '%demo%';

-- 字符集范围匹配与转义
SELECT note_text
FROM productnotes
WHERE note_text LIKE 'version [0-9].[0-9]%'
  AND note_text LIKE '%important%'
  AND note_text NOT LIKE '%\%%';  -- 排除包含%的注释

-- 性能优化：避免前导通配符的替代方案
SELECT prod_name
FROM products
WHERE prod_name LIKE 'anvil%'  -- 可使用索引
  AND REVERSE(prod_name) LIKE 'lvina%';  -- 反转字符串匹配
```

---

### 第九章 用正则表达式进行搜索 ###

#### 正则表达式基础概念 ####

- **正则表达式定义**：用于匹配文本中模式的特殊字符串，提供比LIKE更强大的搜索能力
- **REGEXP操作符**：MySQL中用于执行正则表达式匹配，`WHERE prod_name REGEXP 'pattern'`
- **与LIKE区别**：REGEXP匹配子串，LIKE匹配整个列值；REGEXP更灵活但性能较低
- **大小写敏感性**：MySQL正则表达式默认不区分大小写，可使用BINARY强制区分

#### 基本字符匹配 ####

- **字面值匹配**：直接匹配指定文本，如`REGEXP '1000'`匹配包含"1000"的值
- **点号(.)元字符**：匹配任意单个字符，如`REGEXP '.000'`匹配"1000"、"2000"等
- **OR匹配**：使用竖线|匹配多个模式之一，如`REGEXP '1000|2000'`

#### 字符集合与范围匹配 ####

- **字符集[]**：匹配方括号内任意单个字符，如`REGEXP '[123] Ton'`匹配"1 Ton"、"2 Ton"、"3 Ton"
- **范围匹配**：使用连字符定义范围，如`[0-9]`匹配数字，`[a-z]`匹配小写字母
- **否定字符集**：使用`[^]`匹配不在集合中的字符，如``匹配非1、2、3的字符

#### 重复元字符 ####

- **星号***：匹配前面元素零次或多次，如`REGEXP '\\([0-9] sticks?\\)'`匹配"(1 stick)"或"(5 sticks)"
- **加号+**：匹配前面元素一次或多次（MySQL需转义为`\\+`）
- **问号?**：匹配前面元素零次或一次
- **花括号{}**：指定精确重复次数，如`[0-9]{4}`匹配恰好4位数字

#### 定位元字符 ####

- **脱字符^**：匹配文本开始，如`REGEXP '^[0-9\\.]'`匹配以数字或小数点开头的文本
- **美元符$**：匹配文本结束，如`REGEXP'[0−9]$'`匹配以数字结尾的文本
- **词边界[[:<:]]和[[:>:]]**：匹配词的开始和结束位置

#### 预定义字符类 ####

| 字符类         | 等效表达式    | 描述         |
| -------------- | ------------- | ------------ |
| `[[:alnum:]]`  | `[a-zA-Z0-9]` | 字母数字字符 |
| `[[:alpha:]]`  | `[a-zA-Z]`    | 字母字符     |
| `[[:digit:]]`  | `[0-9]`       | 数字字符     |
| `[[:blank:]]`  | `[ \\t]`      | 空格和制表符 |
| `[[:xdigit:]]` | `[a-fA-F0-9]` | 十六进制数字 |

#### 转义特殊字符 ####

- **反斜杠转义**：使用`\\`转义正则表达式元字符，如`\\.`匹配字面点号
- **常见转义序列**：`\\f`(换页)、`\\n`(换行)、`\\r`(回车)、`\\t`(制表)、`\\v`(垂直制表)
- **MySQL转义要求**：MySQL要求两个反斜杠`\\`，一个用于MySQL解释，一个用于正则表达式引擎

#### 正则表达式函数对比表 ####

| 函数/操作符     | 语法示例                        | 功能描述           | 返回值             |
| --------------- | ------------------------------- | ------------------ | ------------------ |
| REGEXP          | `col REGEXP 'pattern'`          | 检查是否匹配模式   | 1(匹配)或0(不匹配) |
| NOT REGEXP      | `col NOT REGEXP 'pattern'`      | 检查是否不匹配模式 | 1(不匹配)或0(匹配) |
| RLIKE           | `col RLIKE 'pattern'`           | REGEXP的同义词     | 同REGEXP           |
| REGEXP_INSTR()  | `REGEXP_INSTR(col, 'pattern')`  | 返回匹配开始位置   | 位置索引(从1开始)  |
| REGEXP_SUBSTR() | `REGEXP_SUBSTR(col, 'pattern')` | 返回匹配的子串     | 匹配的字符串       |

#### 复杂正则表达式示例 ####

```mysql
-- 匹配特定格式的产品代码：字母+4位数字+可选后缀
SELECT prod_id, prod_name
FROM products
WHERE prod_id REGEXP '^[A-Z]{2,4}[0-9]{4}(-[A-Z0-9]{2,3})?$';

-- 匹配价格格式：美元符号、可选千分位逗号、小数点后两位
SELECT prod_name, prod_price
FROM products
WHERE prod_price REGEXP '^\\$?[0-9]{1,3}(,[0-9]{3})*(\\.[0-9]{2})?$';

-- 匹配包含特定单词边界的描述文本
SELECT note_text
FROM productnotes
WHERE note_text REGEXP '[[:<:]]urgent[[:>:]]'
   OR note_text REGEXP '[[:<:]]important[[:>:]]';

-- 复杂模式：验证电子邮件格式
SELECT cust_name, cust_email
FROM customers
WHERE cust_email REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$';

-- 使用字符类和重复匹配序列号格式
SELECT prod_id, prod_serial
FROM products
WHERE prod_serial REGEXP '^[A-Z0-9]{3}-[0-9]{2}-[A-Z]{2}-[0-9]{6}$';
```

#### 性能优化与最佳实践 ####

- **避免前导通配符**：如`REGEXP '^.*pattern'`会导致全表扫描
- **使用字符类代替点号**：`[0-9]`比`.`更精确且可能使用索引
- **合理使用锚点**：`^`和`$`可以显著减少匹配范围
- **考虑全文搜索替代**：对于复杂文本搜索，考虑使用MATCH() AGAINST()

---

### 第十章 创建计算字段 ###

#### 计算字段概念与用途 ####

- **计算字段定义**：运行时在SELECT语句内创建，不在数据库表中实际存储
- **应用场景**：格式化检索数据、执行算术计算、拼接多个列值
- **与应用程序处理对比**：在数据库服务器执行计算通常比在客户端更快
- **字段别名**：使用AS关键字为计算字段命名，便于客户端引用

#### 字段拼接技术 ####

- **Concat()函数**：MySQL中用于拼接字符串，语法`CONCAT(str1, str2, ...)`
- **Trim函数家族**：RTrim()去除右侧空格，LTrim()去除左侧空格，Trim()去除两侧空格
- **NULL值处理**：拼接时遇到NULL值会导致整个结果为NULL，需用IFNULL处理
- **易错点**：不同DBMS使用+或||拼接，MySQL必须用CONCAT()

#### 算术计算操作 ####

- **基本算术操作符**：+（加）、-（减）、*（乘）、/（除）
- **计算优先级**：乘除优先于加减，可用括号改变顺序
- **数值精度**：计算时注意数据类型和精度，避免意外截断
- **除零保护**：除数为零会导致错误，需预先检查

#### 拼接与计算函数对比表 ####

| 函数/操作   | 语法示例                              | 功能描述     | NULL值处理               |
| ----------- | ------------------------------------- | ------------ | ------------------------ |
| CONCAT()    | `CONCAT(col1, '(', col2, ')')`        | 字符串拼接   | 任一参数为NULL则返回NULL |
| CONCAT_WS() | `CONCAT_WS('-', col1, col2)`          | 带分隔符拼接 | 忽略NULL值，保留分隔符   |
| 算术运算    | `col1 * col2`或 `(col1 + col2) * 0.1` | 数值计算     | NULL参与运算结果为NULL   |
| IFNULL()    | `IFNULL(col, 'N/A')`                  | NULL值替换   | 将NULL替换为指定值       |

#### 复杂计算字段示例 ####

```mysql
-- 多列拼接与格式化，处理可能的NULL值
SELECT 
    CONCAT(RTrim(vend_name), ' (', RTrim(vend_country), ')') AS vend_title,
    CONCAT_WS(' - ', vend_city, IFNULL(vend_state, 'N/A')) AS vend_location,
    prod_price * 0.9 AS discounted_price,
    (prod_price * quantity) / 
        CASE WHEN discount_rate > 0 THEN discount_rate ELSE 1 END 
    AS net_value
FROM vendors, products
WHERE vendors.vend_id = products.vend_id;

-- 条件计算与复杂格式化
SELECT 
    order_num,
    CONCAT('Item: ', prod_id, ' Qty: ', quantity) AS order_description,
    quantity * item_price AS subtotal,
    quantity * item_price * 
        CASE 
            WHEN quantity > 10 THEN 0.9  -- 数量折扣
            WHEN customer_type = 'VIP' THEN 0.85  -- VIP折扣
            ELSE 1.0 
        END AS final_price
FROM orderitems;
```

------

### 第十一章 使用数据处理函数 ###

#### 函数分类与基本概念 ####

- **文本处理函数**：处理字符串数据，如大小写转换、去除空格等
- **日期时间函数**：处理日期和时间值，支持格式化、计算等
- **数值处理函数**：执行代数、三角或几何运算
- **系统函数**：返回DBMS特定信息，如版本、用户等
- **可移植性问题**：不同DBMS函数差异大，MySQL函数在其他系统中可能不工作

#### 文本处理函数详解 ####

- **大小写转换**：Upper()转大写，Lower()转小写
- **长度与修剪**：Length()返回长度，Trim()系列去除空格
- **子串操作**：SubString()提取子串，Locate()查找位置
- **Soundex函数**：将文本转换为描述其语音的代码，用于发音匹配

#### 日期时间处理函数 ####

- **当前时间**：Now()返回当前日期时间，CurDate()返回当前日期，CurTime()返回当前时间
- **日期部分提取**：Year()、Month()、Day()等提取日期组成部分
- **日期计算**：Date_add()、Date_sub()进行日期加减，Datediff()计算日期差
- **日期格式化**：Date_format()按指定格式显示日期

#### 数值处理函数 ####

- **基本数学函数**：Abs()绝对值，Round()四舍五入，Mod()求余
- **三角函数**：Sin()、Cos()、Tan()等
- **指数对数**：Exp()指数，Log()对数，Pow()幂运算
- **随机数**：Rand()生成随机数

#### 常用函数分类表 ####

| 函数类别 | 常用函数示例                     | 主要用途   | MySQL特性        |
| -------- | -------------------------------- | ---------- | ---------------- |
| 文本函数 | CONCAT(), SUBSTRING(), REPLACE() | 字符串操作 | 支持多字节字符集 |
| 日期函数 | NOW(), DATE_FORMAT(), DATEDIFF() | 时间处理   | 灵活的日期格式   |
| 数值函数 | ROUND(), ABS(), RAND()           | 数学计算   | 高精度数值处理   |
| 系统函数 | VERSION(), USER(), DATABASE()    | 系统信息   | 返回服务器状态   |

#### 函数使用注意事项 ####

- **性能考虑**：在WHERE子句中使用函数可能导致无法使用索引
- **NULL处理**：大多数函数遇到NULL参数返回NULL
- **时区问题**：日期时间函数受服务器时区设置影响
- **字符集影响**：文本函数结果依赖数据库字符集配置

---

### 第十二章 汇总数据 ###

#### 聚集函数概念与用途 ####

- **聚集函数定义**：对一组值执行计算，返回单个汇总值
- **应用场景**：统计行数、计算平均值、求和、找最大最小值等
- **NULL值处理**：除COUNT(*)外，所有聚集函数忽略NULL值
- **性能优势**：在数据库服务器执行汇总比在应用程序中处理更高效

#### 主要聚集函数详解 ####

- **AVG()函数**：计算某列平均值，`AVG(column)`，只用于数值列
- **COUNT()函数**：计数，`COUNT(*)`统计所有行，`COUNT(column)`统计非NULL值
- **MAX()/MIN()函数**：返回列的最大/最小值，适用于数值、文本、日期类型
- **SUM()函数**：计算列值总和，只用于数值列

#### 聚集函数特性对比表 ####

| 函数    | 语法                         | 功能描述   | NULL处理                                      | 适用数据类型     |
| ------- | ---------------------------- | ---------- | --------------------------------------------- | ---------------- |
| AVG()   | `AVG(column)`                | 计算平均值 | 忽略NULL                                      | 数值型           |
| COUNT() | `COUNT(*)`或 `COUNT(column)` | 统计行数   | `COUNT(*)`计数所有行，`COUNT(column)`忽略NULL | 任意             |
| MAX()   | `MAX(column)`                | 返回最大值 | 忽略NULL                                      | 数值、文本、日期 |
| MIN()   | `MIN(column)`                | 返回最小值 | 忽略NULL                                      | 数值、文本、日期 |
| SUM()   | `SUM(column)`                | 计算总和   | 忽略NULL                                      | 数值型           |

#### DISTINCT在聚集函数中的应用 ####

- **去重汇总**：`AVG(DISTINCT column)`只对唯一值计算
- **计数唯一值**：`COUNT(DISTINCT column)`统计不同值的数量
- **性能考虑**：DISTINCT增加计算开销，大数据集慎用
- **易错点**：DISTINCT必须紧接函数名后，如`COUNT(DISTINCT vend_id)`

#### 复杂汇总示例 ####

```MySQL
-- 多表联接与条件聚集
SELECT 
    c.cust_name,
    COUNT(DISTINCT o.order_num) AS order_count,
    AVG(oi.quantity) AS avg_items_per_order,
    SUM(oi.quantity * oi.item_price) AS total_spent,
    MAX(o.order_date) AS last_order_date,
    DATEDIFF(NOW(), MAX(o.order_date)) AS days_since_last_order
FROM customers c
JOIN orders o ON c.cust_id = o.cust_id
JOIN orderitems oi ON o.order_num = oi.order_num
WHERE o.order_date > '2023-01-01'
GROUP BY c.cust_id, c.cust_name
HAVING COUNT(DISTINCT o.order_num) > 1
   AND SUM(oi.quantity * oi.item_price) > 100;

```

#### 注意事项

1. **COUNT(*) VS COUNT(列)**

​	**COUNT(\*)**：计算所有行数，包括所有列都为NULL的行

​	**COUNT(列名)**：只计算该列**非NULL值**的行数

​	**特殊情况：** 如果某列所有值都非NULL，则两者结果相同。

2. **COUNT函数的统计对象：剩余某列的所有行？**

   COUNT(*) 数的不是"仅剩的一列"，而是**每个分组中的总行数**。

   **本质知识点：**

   1. **分组统计** - 按 vend_id 和 prod_price 的组合值分组
   2. **行数计算** - COUNT(*) 统计每个分组包含多少行记录
   3. **多列分组** - 按多个列的组合值创建分组单元

------

### 第十三章 分组数据 ###

#### GROUP BY子句核心概念 ####

- **分组目的**：将数据划分为逻辑组，==对每个组==执行聚集计算
- **语法结构**：`GROUP BY column1, column2, ...`
- **与ORDER BY区别**：GROUP BY分组数据，ORDER BY排序结果
- **NULL值分组**：NULL值会被分为一组

#### GROUP BY核心规则详解 ####

**1. 多列分组能力**

- GROUP BY后面可以跟多个列，用逗号分隔
- 实现多层次分组，比如先按省份分组，再按城市分组
- 提供更精细的数据分组控制

**2. 分组层级规则**

- 有多列时，按**最后一列**进行最终汇总
- 所有指定列一起参与分组计算
- 不能单独获取某个中间层次的分组数据

**3. 列表达式要求**

- GROUP BY中的列必须是SELECT中的实际列或表达式
- 如果SELECT中使用表达式，GROUP BY也要用相同表达式
- **不能使用别名**，必须用原始表达式

**示例**：

```MySQL
-- ✅ 正确
SELECT YEAR(order_date), COUNT(*) 
FROM orders 
GROUP BY YEAR(order_date);

-- ❌ 错误（不能用别名）
SELECT YEAR(order_date) AS order_year, COUNT(*) 
FROM orders 
GROUP BY order_year;  -- 错误！
```

**4. 非聚集列规则**

- SELECT中的每个**非聚集函数列**都必须在GROUP BY中出现
- 只有聚集函数（SUM、AVG等）可以不在GROUP BY中

**示例**：

```MySQL
-- ✅ 正确
SELECT vend_id, COUNT(*) FROM products GROUP BY vend_id;

-- ❌ 错误（vend_name不在GROUP BY中）
SELECT vend_id, vend_name, COUNT(*) FROM products GROUP BY vend_id;
```

**5. NULL值处理**

- NULL值会被视为一个独立的分组
- 所有NULL值会分到同一组
- NULL不是"没有值"，而是"未知值"分组

**6. 子句顺序要求**

- **必须**的顺序：WHERE → GROUP BY → ORDER BY
- 先过滤行（WHERE），再分组（GROUP BY），最后排序（ORDER BY）

**完整示例**：

```MySQL
SELECT vend_id, COUNT(*) as product_count
FROM products 
WHERE prod_price > 10          -- 1. 先过滤
GROUP BY vend_id               -- 2. 再分组  
ORDER BY product_count DESC;   -- 3. 最后排序
```

#### HAVING子句过滤分组 ####

- **与WHERE区别**：WHERE过滤行，HAVING过滤分组
- **使用时机**：HAVING在数据分组后过滤，WHERE在分组前过滤
- **性能考虑**：优先用WHERE过滤，减少HAVING处理的数据量
- **语法位置**：GROUP BY之后，ORDER BY之前

#### 分组与过滤对比表 ####

| 子句     | 执行顺序  | 过滤对象 | 可使用聚集函数 | 主要用途     |
| -------- | --------- | -------- | -------------- | ------------ |
| WHERE    | 分组前    | 行       | 否             | 初步数据过滤 |
| GROUP BY | WHERE之后 | 创建分组 | 否             | 数据分组     |
| HAVING   | 分组后    | 分组     | 是             | 分组结果过滤 |
| ORDER BY | 最后      | 排序结果 | 是             | 结果排序     |

#### WITH ROLLUP分组 ####

- **小计功能**：`GROUP BY ... WITH ROLLUP`生成层次化小计
- **NULL标识**：汇总行在分组列显示NULL
- **多级汇总**：按GROUP BY列顺序生成多级小计
- **结果排序**：汇总行出现在各组之后

#### 分组性能优化 ####

- **索引利用**：GROUP BY列有索引可显著提高性能
- **过滤顺序**：先用WHERE减少数据量，再进行分组
- **避免过度分组**：只对必要的列分组
- **内存设置**：大数据集分组可能需要调整group_concat_max_len等参数

#### 注意事项

1. **区分对列/行/多行/分组的操作**

| 关键字/函数  | 操作对象 | 输出对象     |
| ------------ | -------- | ------------ |
| **普通函数** | 单行值   | 单行值       |
| **聚集函数** | 多行(列) | 单个值       |
| **GROUP BY** | 多行     | 多个分组     |
| **WHERE**    | 行       | 过滤后的行   |
| **HAVING**   | 分组     | 过滤后的分组 |

**核心规律**：先确定操作对象（行/列/分组），再选择对应工具！

---

### 第十四章 使用子查询 ###

#### 子查询基本概念 ####

- **子查询定义**：嵌套在其他查询中的SELECT语句，用于从查询结果中检索数据
- **应用场景**：作为过滤条件、计算字段、表数据源等
- **执行顺序**：从最内层子查询开始执行，结果传递给外层查询
- **性能考虑**：复杂的嵌套子查询可能影响性能，需合理使用

#### 子查询类型与使用方式 ####

- **标量子查询**：返回单个值的子查询，可用于SELECT列表、WHERE条件等
- **列子查询**：返回单列多行的子查询，常与IN、ANY、ALL操作符使用
- **行子查询**：返回单行多列的子查询，需与行构造器配合使用
- **表子查询**：返回完整表的子查询，可用作FROM子句中的派生表

#### 子查询操作符对比表 ####

| 操作符   | 语法示例                        | 功能描述                   | 适用子查询类型 |
| -------- | ------------------------------- | -------------------------- | -------------- |
| IN       | `WHERE col IN (SELECT ...)`     | 检查值是否在子查询结果中   | 列子查询       |
| NOT IN   | `WHERE col NOT IN (SELECT ...)` | 检查值是否不在子查询结果中 | 列子查询       |
| ANY/SOME | `WHERE col > ANY (SELECT ...)`  | 与子查询结果的任一值比较   | 列子查询       |
| ALL      | `WHERE col > ALL (SELECT ...)`  | 与子查询结果的所有值比较   | 列子查询       |
| EXISTS   | `WHERE EXISTS (SELECT ...)`     | 检查子查询是否返回结果     | 相关子查询     |

#### 子查询在WHERE子句中的应用 ####

```MySQL
-- 使用IN操作符过滤订单
SELECT cust_id, cust_name
FROM customers
WHERE cust_id IN (
    SELECT DISTINCT cust_id
    FROM orders
    WHERE order_date >= '2023-01-01'
);

-- 使用比较操作符与标量子查询
SELECT prod_name, prod_price
FROM products
WHERE prod_price > (
    SELECT AVG(prod_price)
    FROM products
    WHERE vend_id = 1003
);

-- 使用EXISTS检查相关记录
SELECT vend_name
FROM vendors v
WHERE EXISTS (
    SELECT 1
    FROM products p
    WHERE p.vend_id = v.vend_id
    AND p.prod_price > 100
);
```

#### 子查询在SELECT子句中的应用 ####

```MySQL
-- 作为计算字段使用
SELECT 
    cust_name,
    cust_state,
    (SELECT COUNT(*) 
     FROM orders o
     WHERE o.cust_id = c.cust_id) AS order_count,
    (SELECT SUM(quantity * item_price)
     FROM orderitems oi
     JOIN orders o ON oi.order_num = o.order_num
     WHERE o.cust_id = c.cust_id) AS total_spent
FROM customers c
ORDER BY total_spent DESC;

-- 多列子查询示例
SELECT 
    order_num,
    order_date,
    (SELECT cust_name FROM customers c WHERE c.cust_id = o.cust_id) AS customer_name,
    (SELECT vend_name FROM vendors v 
     JOIN products p ON v.vend_id = p.vend_id
     JOIN orderitems oi ON p.prod_id = oi.prod_id
     WHERE oi.order_num = o.order_num LIMIT 1) AS primary_vendor
FROM orders o
WHERE order_date > '2023-01-01';
```

#### 相关子查询与非相关子查询 ####

- **非相关子查询**：子查询独立于外层查询，可单独执行
- **相关子查询**：子查询引用外层查询的列，需与外层查询结合执行
- **性能差异**：相关子查询通常性能较差，因需为每行执行一次
- **使用场景**：相关子查询适合检查存在性、计算相关聚合等

#### 子查询性能优化技巧 ####

- **避免过度嵌套**：多层嵌套子查询难以理解和优化
- **使用JOIN替代**：某些子查询可用JOIN重写，提高性能
- **限制结果集**：在子查询中使用LIMIT或WHERE减少数据处理量
- **索引利用**：确保子查询中使用的列有适当索引

#### 常见子查询错误与解决方法 ####

- **单行子查询返回多行**：使用聚合函数或LIMIT确保返回单行
- **列数不匹配**：确保比较操作符两侧列数一致
- **NULL值问题**：NOT IN子查询包含NULL时返回空结果
- **性能问题**：使用EXPLAIN分析查询计划，优化索引

#### 注意事项

1. **硬编码的风险**

   ​	**硬编码** 指的是在程序或SQL语句中，直接写入具体的、固定的数据值，而不是动态地获取这些值。硬编码的主要问题在于它将数据与逻辑捆绑在一起，导致代码**脆弱、不灵活且难以维护**。

   1. **数据失效风险高**：如果ID为10004的客户被删除，第一条硬编码的查询将永远无法再返回该客户的数据，即使未来有新的符合条件（购买了TNT2）的客户（比如10007），查询结果也不会包含他。而子查询会**始终基于数据库的最新状态**动态生成ID列表。
   2. **维护成本高**：当业务逻辑变化时（例如，想查找购买的是“TNT1”而非“TNT2”的客户），你必须： **手动找出**哪些ID符合新条件。 **小心地修改**SQL语句中的ID列表。 这个过程极易出错且效率低下。而使用子查询，你只需修改一个条件（`prod_id = 'TNT1'`）即可。
   3. **违背自动化原则**：数据库系统的强大之处在于自动处理数据。硬编码需要人工干预和预先知晓结果，这使得查询无法自适应数据的变化。



2. **IN子查询 vs 相关子查询**

   一个简单的判断标准：**“子查询能独立运行并得出结果吗？”**

   - **如果能 ⇒ 使用 `IN`的子查询（非相关子查询）**
   - **如果不能 ⇒ 使用相关子查询**

   **应用 `IN`子查询的场景（列表过滤）**

   **核心思想：先内后外。** 子查询自己就能生成一个完整的、静态的“值列表”，外部查询用这个列表来过滤。

   - **场景特征**：你要找的数据，是基于一个**明确的、可预先确定的清单**。
   - **如何判断**：把子查询部分 `(SELECT ...)`单独拿出来执行，它能返回一个有效的结果列表。

   **应用相关子查询的场景（逐行校验）**

   **核心思想：先外后内，循环处理。** 子查询的查询条件**依赖于**外部查询当前行的值。它必须为外部查询的每一行都执行一次，像一个“校验器”。

   - **场景特征**：你要对**外部查询的每一行数据**，都进行一次额外的检查或计算。
   - **如何判断**：把子查询单独拿出来执行会报错（因为它引用了外部表的列，如 `a.id`），或者结果毫无意义。



3. **相关子查询及其时间复杂度O(n^2^)**

​	相关子查询的精髓在于 **“外部查询的每一行，都会触发一次内部子查询的执行”**。它与普通子查询的关键区别是：**子查询的WHERE条件中，引用了外部查询的列**。这使得子查询的结果依赖于外部查询的当前行。

​	假设我们有两个表：`orders`（订单表）和 `customers`（客户表）。**目标：** 找出那些订单总金额超过2000的“大客户”的姓名。

```MySQL
SELECT cust_name
FROM customers
WHERE 2000 < (
    SELECT SUM(item_price * quantity)
    FROM orderitems
    WHERE orderitems.order_num IN (
        SELECT orders.order_num
        FROM orders
        WHERE orders.cust_id = customers.cust_id -- 关键！这里引用了外部查询的cust_id
    )
);
```

1. 数据库从 `customers`表取出**第一个客户**（比如 `cust_id = 10001`）。
2. 开始执行子查询：“计算这个特定客户（`cust_id = 10001`）的所有订单总金额”。
3. 将计算出的总金额与2000比较。如果大于2000，第一行（客户10001）放入结果集。
4. 数据库回到 `customers`表，取出**第二个客户**（`cust_id = 10002`）。
5. **重新执行**子查询：“计算这个**新客户**（`cust_id = 10002`）的所有订单总金额”。
6. 重复比较，决定是否放入结果集。
7. ... 如此循环，直到 `customers`表的每一行都处理完毕。

​	**时间复杂度是O(N²)吗？是的，在绝大多数情况下，它的时间复杂度确实是 O(N \* M)，这属于O(N²)级别。**

- **N** 是外部查询的行数（例如，客户数量）。
- **M** 是子查询需要扫描的平均行数（例如，订单表的行数）。

​	如果有1000个客户和10000个订单，最坏情况下，子查询需要执行1000次，每次平均扫描10000行，总共就是1000万次操作，性能代价非常高。



4. **WHERE子句使用子查询，SELECT语句与WHERE子句列数目相同**

​	**因为大多数在WHERE子句中使用子查询的情况，都是为了进行“比较”（=, IN, >等）。而比较运算要求两边的元素数量必须一致。**

```MySQL
-- 错误示范
SELECT 姓名 FROM 学生表
WHERE 学生ID IN (
    SELECT 学生ID, 课程 -- 子查询SELECT了2列：学生ID 和 课程
    FROM 成绩表
    WHERE 分数 > 95
);
-- 正确示范
SELECT 姓名 FROM 学生表
WHERE 学生ID IN (
    SELECT 学生ID -- 子查询只SELECT了1列：学生ID
    FROM 成绩表
    WHERE 分数 > 95
);
```

​	 `WHERE 学生ID IN (...)`的左边是单个值（一列：`学生ID`），但子查询却返回了两列（`学生ID`和 `课程`）。数据库引擎会困惑：到底是想用`学生ID`和`学生ID`比较，还是用`学生ID`和`课程`比较？——问题本身是无意义的。只有两边数量一致，比较才可以进行。它的逻辑很清晰：“在学生表中找出那些ID，存在于（分数大于95分的学生ID列表）中。”

**核心要点总结**：

| 场景                   | WHERE子句                                    | 子查询应返回                                   | 比较逻辑                                         |
| ---------------------- | -------------------------------------------- | ---------------------------------------------- | ------------------------------------------------ |
| **单值比较**           | `列A = (...)`                                | **必须只有1列1行**                             | 检查列A的值是否等于子查询返回的那个唯一值。      |
| **多值比较（最常用）** | `列A IN (...)`                               | **必须只有1列（但可以有多个行）**              | 检查列A的值是否在子查询返回的那一列值之中。      |
| **极少用的多列比较**   | `(列A, 列B) = (...)`或 `(列A, 列B) IN (...)` | **必须返回与左边括号内数量、顺序完全一致的列** | 检查列A和列B的组合是否与子查询返回的列组合匹配。 |

---

### 第十五章 联结表 ###

#### 关系数据库与联结基础 ####

- **关系表设计**：信息分解为多个表，通过共同值（键）互相关联
- **主键与外键**：主键唯一标识行，外键是另一个表的主键
- **可伸缩性**：关系数据库比非关系数据库更适应数据量增长
- **数据一致性**：避免重复存储，减少数据不一致风险

#### 创建联结的基本语法 ####

- **WHERE子句联结**：指定联结条件，`WHERE table1.column = table2.column`
- **完全限定列名**：使用`table.column`格式避免歧义
- **笛卡尔积**：没有WHERE子句时返回表的笛卡尔积（行数乘积）
- **内部联结**：只返回两个表中匹配的行

#### 联结类型对比表 ####

| 联结类型 | 语法示例                | 返回结果     | 适用场景       |
| -------- | ----------------------- | ------------ | -------------- |
| 等值联结 | `WHERE t1.col = t2.col` | 匹配列值的行 | 大多数联结场景 |
| 内部联结 | `INNER JOIN ... ON`     | 同等值联结   | 标准SQL语法    |
| 多表联结 | 多个WHERE条件或JOIN     | 多表关联数据 | 复杂数据关系   |

#### 基本联结示例 ####

```MySQL
-- 等值联结：供应商与其产品
SELECT vend_name, prod_name, prod_price
FROM vendors, products
WHERE vendors.vend_id = products.vend_id;

-- 内部联结语法（ANSI标准）
SELECT vend_name, prod_name, prod_price
FROM vendors INNER JOIN products
ON vendors.vend_id = products.vend_id;

-- 多表联结：订单详情
SELECT prod_name, vend_name, prod_price, quantity
FROM orderitems, products, vendors
WHERE products.vend_id = vendors.vend_id
AND orderitems.prod_id = products.prod_id
AND order_num = 20005;
```

#### 联结性能与最佳实践 ####

- **WHERE子句重要性**：缺少WHERE条件会产生笛卡尔积
- **完全限定名**：多表联结时必须使用表名限定列名
- **联结顺序**：MySQL优化器通常会自动选择最佳联结顺序
- **索引利用**：联结列上的索引显著提高性能

#### 注意事项

1. **可伸缩性 Scalability**

   评估SQL数据库的可伸缩性，主要从两个维度考量：

   1. **水平扩展**：通过增加服务器节点来分散负载，常用技术是**分片（Sharding）**。评估关键在于分片机制的效率、数据分布均衡性，以及在扩展后能否维持**数据一致性**。
   2. **垂直扩展**：通过提升单机硬件资源（如CPU、内存）来增强性能。评估重点是数据库软件对硬件资源的利用效率及可能遇到的性能瓶颈。

   实际操作中，需使用**基准测试工具（如BenchmarkSQL）模拟高并发负载**，观察系统在增加资源（节点或硬件）后，**吞吐量是否线性增长、响应时间是否稳定**。



2. **叉联结 Cross Join**

   叉联结（CROSS JOIN）是返回参与联结的所有表中所有行的**笛卡尔积**。

   这意味着，如果第一个表有 M 行，第二个表有 N 行，结果将产生 M × N 行。它不基于任何关联条件（即没有 ON 或 USING 子句），只是简单地将一个表的每一行与另一个表的每一行进行组合。

   在实际应用中，叉联结**通常是无意中**因漏写联结条件导致，会造成结果集急剧扩大，性能极差。其**有意用途**很少见，例如为所有产品生成所有尺寸的组合列表。



3. **INNER JOIN …… ON …… 与WHERE……**

   简而言之，在处理**INNER JOIN**（内联结）时，从**结果上来看**，`INNER JOIN ... ON`中的条件 和 `WHERE`子句中的条件**是等效的**，查询优化器通常会以相同方式执行，最终返回完全相同的数据集。

   它们的主要区别在于**语义清晰度和可维护性**：

   1. **`ON`子句的作用**：专门用于指定**表之间的联结条件**，即定义这些表是如何关联在一起的（例如 `tableA.column = tableB.column`）。它回答“表如何连接”的问题。
   2. **`WHERE`子句的作用**：用于在联结后形成的中间结果集上进行**过滤**。它回答“需要哪些行”的问题。



4. **目前为止的SQL操作，以及选取合适的方法**
   1. **数据检索与过滤** **操作**：匹配复杂条件。 **方法**：`OR`组合条件 与 `IN`操作符。 **选择**：`IN`语法更清晰，执行效率通常更高，尤其适合值列表很长的情况。
   2. **通配符与正则表达式** **操作**：进行模式匹配。 **方法**：`LIKE`与 `REGEXP`。 **选择**：简单前缀匹配（如 `abc%`）用 `LIKE`。复杂模式（如“包含abc或xyz”）用 `REGEXP`更强大灵活。
   3. **从多表检索数据** **操作**：基于关系查询数据。 **方法**：**子查询** 与 **联结（JOIN）**。 **选择**：这是最重要的选择。**联结**（特别是与聚集函数结合时）通常性能远优于子查询，是处理关联数据的首选和标准方式。子查询在逻辑上更直观，适合作为过滤条件。
   4. **结果排序与分组** **操作**：组织结果集。 **方法**：`ORDER BY`与 `GROUP BY`。 **选择**：目的不同，不互相替代。`ORDER BY`纯粹用于排序；`GROUP BY`用于分组并计算聚集值（如SUM, COUNT）。但注意，`GROUP BY`会按分组列排序，若需不同顺序仍要显式使用 `ORDER BY`。
   5. **编写联结** **操作**：指定表如何关联。 **方法**：`WHERE`子句 与 `INNER JOIN ... ON`标准语法。 **选择**：**强烈推荐 `INNER JOIN ... ON`**。它将联结条件与过滤条件（`WHERE`）分离，语法更清晰、更符合标准，可读性和可维护性更好，能有效避免产生笛卡尔积错误。

------

### 第十六章 创建高级联结 ###

#### 表别名使用 ####

- **创建别名**：`FROM table AS alias`或 `FROM table alias`
- **列别名vs表别名**：列别名用于输出，表别名用于简化SQL语句
- **作用范围**：表别名仅在查询执行期间存在
- **易错点**：表别名不能用于查询外部

#### 自联结技术 ####

- **自联结概念**：同一个表与自己联结
- **应用场景**：查找同一表中相关的行（如员工和经理）
- **与子查询对比**：通常比子查询性能更好
- **别名必要性**：必须使用不同表别名区分实例

```mysql
-- 查找同一供应商的不同产品
SELECT p1.prod_id, p1.prod_name
FROM products p1, products p2
WHERE p1.vend_id = p2.vend_id
AND p1.prod_id <> p2.prod_id;

-- 使用自联结替代子查询
SELECT p1.prod_id, p1.prod_name
FROM products p1
WHERE p1.vend_id IN (SELECT vend_id FROM products WHERE prod_id = 'DTNTR');
-- 可重写为：
SELECT p1.prod_id, p1.prod_name
FROM products p1, products p2
WHERE p1.vend_id = p2.vend_id
AND p2.prod_id = 'DTNTR';
```

#### 自然联结 ####

- **自然联结定义**：排除重复列的内部联结
- **实现方式**：使用通配符和明确的列子集
- **优势**：每个列只返回一次，结果更清晰
- **限制**：需要人工确保列唯一性

```MySQL
-- 使用通配符和明确列子集避免重复
SELECT c.*, o.order_num, o.order_date, 
       oi.prod_id, oi.quantity, oi.item_price
FROM customers c, orders o, orderitems oi
WHERE c.cust_id = o.cust_id
AND o.order_num = oi.order_num;

-- 等效的内部联结（显示所有列，包括重复的cust_id）
SELECT *
FROM customers c INNER JOIN orders o ON c.cust_id = o.cust_id
INNER JOIN orderitems oi ON o.order_num = oi.order_num;
```

#### 外部联结 ####

- **左外部联结**：返回左表所有行，右表匹配行
- **右外部联结**：返回右表所有行，左表匹配行
- **全外部联结**：返回两个表的所有行（MySQL不支持）
- **匹配规则**：无匹配的行显示NULL值

```MySQL
-- 左外部联结：所有客户及其订单（包括无订单客户）
SELECT customers.cust_id, orders.order_num
FROM customers LEFT OUTER JOIN orders
ON customers.cust_id = orders.cust_id;

-- 右外部联结：所有订单及其客户（包括无效客户ID）
SELECT customers.cust_id, orders.order_num
FROM customers RIGHT OUTER JOIN orders
ON customers.cust_id = orders.cust_id;

-- 多表外部联结
SELECT customers.cust_name, orders.order_num, 
       orderitems.prod_id, orderitems.quantity
FROM customers 
LEFT OUTER JOIN orders ON customers.cust_id = orders.cust_id
LEFT OUTER JOIN orderitems ON orders.order_num = orderitems.order_num;
```

#### 高级联结类型对比表 ####

| 联结类型   | 语法示例                             | 返回结果              | NULL处理       |
| ---------- | ------------------------------------ | --------------------- | -------------- |
| 自联结     | `SELECT ... FROM table t1, table t2` | 同一表内相关行        | 无特殊处理     |
| 自然联结   | `SELECT t1.*, t2.other_col`          | 去除重复列            | 同内部联结     |
| 左外部联结 | `LEFT OUTER JOIN`                    | 左表所有行+匹配右表行 | 无匹配显示NULL |
| 右外部联结 | `RIGHT OUTER JOIN`                   | 右表所有行+匹配左表行 | 无匹配显示NULL |

#### 带聚集函数的联结 ####

```MySQL
-- 统计每个客户的订单数（包括无订单客户）
SELECT customers.cust_name, 
       customers.cust_id, 
       COUNT(orders.order_num) AS num_ord
FROM customers LEFT OUTER JOIN orders
ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_id;

-- 使用联结和聚集函数计算汇总
SELECT vendors.vend_name, 
       COUNT(products.prod_id) AS num_prods,
       AVG(products.prod_price) AS avg_price
FROM vendors LEFT JOIN products
ON vendors.vend_id = products.vend_id
GROUP BY vendors.vend_id;
```

#### 高级联结使用技巧 ####

- **联结条件位置**：ON子句指定联结条件，WHERE子句过滤结果
- **性能考虑**：外部联结通常比内部联结成本高
- **NULL值处理**：外部联结中需处理可能出现的NULL值
- **多表联结顺序**：复杂的多表联结需测试不同顺序的性能

#### 注意事项

1. **为何能排除重复的列？**

   假设 `customers`表和 `orders`表都有 `cust_id`列。使用标准内部联结：

   ```MySQL
   SELECT * FROM customers INNER JOIN orders ON customers.cust_id = orders.cust_id;
   ```

   结果会同时出现 `customers.cust_id`和 `orders.cust_id`两列（内容相同），造成数据冗余。

   **为何书中例子能排除重复列**

   1. **只对第一个表使用通配符**：`c.*`仅展开 `customers`表的唯一列集合，不会重复其他表的列名。
   2. **其他表列手动指定**：后续明确列出 `o.order_num, o.order_date...`时，直接避开了 `orders`表中已与 `customers`表重复的 `cust_id`列，同时精准选择所需列。



2. **INNER与OUTER默认左表通过关联行连结到右表吗？**

   **INNER JOIN 的匹配逻辑**

   ​	INNER JOIN 没有默认的“左表连结到右表”方向性。它严格基于关联条件，**同时匹配两表的行**。若左表某行在右表无对应，则该行被排除，反之亦然。本质是取两表的**交集**。

   **OUTER JOIN 的方向性**

   OUTER JOIN 具有明确方向性：

   - **LEFT OUTER JOIN**：默认以左表为基准，返回左表所有行+右表匹配行（无匹配则右表字段为NULL）。
   - **RIGHT OUTER JOIN**：以右表为基准，返回右表所有行+左表匹配行。
   - **FULL OUTER JOIN**（MySQL不支持）：返回两表所有行，无匹配处补NULL。



3. **关于连结**

   **连结本质**：JOIN 操作确实先形成表的笛卡尔积（所有行组合），然后通过 ON 或 WHERE 条件过滤出有效行。但现代 SQL 推荐使用 ON 指定连结条件，WHERE 用于后过滤。

   **列名保留**：结果集中的列**默认保留原始表名.列名**，尤其当多表有重名列时，必须用完全限定名区分。

   **GROUP BY 支持**：GROUP BY 完全可以基于这些完全限定列名（如 `customers.cust_id`）分组，避免歧义。

---

### 第十七章 组合查询（UNION） ###

#### **核心概念** ####

​	组合查询允许你执行多个`SELECT`查询，并将结果合并成一个结果集。它主要用于两种场景：1）在单个查询中从不同的表返回结构相似的数据；2）对单个表执行多个查询，按单个查询返回数据。其本质是执行多个查询后，将结果集进行“并”操作。

#### **关键语法与操作** ####

- **创建组合查询**：使用`UNION`关键字连接多个`SELECT`语句。 

  ```MySQL
  SELECT vend_id, prod_id, prod_price FROM products WHERE prod_price <= 5 UNION SELECT vend_id, prod_id, prod_price FROM products WHERE vend_id IN (1001, 1002);
  ```

- **UNION规则**： `UNION`必须由两条或两条以上的`SELECT`语句组成。 每个`SELECT`查询必须包含相同数量、相同类型的列。 ==列的数据类型必须兼容,但来源的列名不要求相同==。

- **包含或取消重复的行**： `UNION`会自动取消重复的行（这是默认行为，类似于多个`WHERE`子句用`OR`连接）。 使用`UNION ALL`可以返回所有匹配行，包括重复的行。这在需要匹配所有行或确知不会有重复时，能提升性能，因为它省去了去重步骤。

- **对组合查询结果排序**：`ORDER BY`子句只能出现一次，且必须位于最后一条`SELECT`语句之后。它将应用于所有`SELECT`语句返回的最终结果集。 

  ```MySQL
  SELECT ... UNION SELECT ... ORDER BY column_name;
  ```

#### **易错点与技巧** ####

| 关键字/概念           | 说明/易错点                                                  |
| --------------------- | ------------------------------------------------------------ |
| **`UNION`vs `WHERE`** | 多数`UNION`查询可以用包含多个`WHERE`条件的单条查询重写。应根据性能和可读性决定使用哪种。 |
| **`ORDER BY`位置**    | 在组合查询中，`ORDER BY`必须位于最后。不能为部分`SELECT`语句单独排序。 |
| **列名**              | `ORDER BY`中使用的列名通常是第一个`SELECT`查询中的列名（或别名）。 |
| **`UNION ALL`**       | 当需要包含重复行或确定无重复时，使用`UNION ALL`性能更优。    |

#### 注意事项

1. **UNION ALL与UNION的对象与搭配**

   ​	在组合查询中，每个SELECT语句之间都需要明确指定连接方式。可以出现多个UNION ALL，也可以混合使用UNION和UNION ALL。

   ```MySQL
   SELECT ... 
   UNION ALL
   SELECT ...
   UNION ALL  -- 第二个UNION ALL
   SELECT ...
   UNION      -- 这里改用UNION去重
   SELECT ...
   ```

   **规则说明：**

   - 每个UNION/UNION ALL只连接相邻的两个SELECT语句
   - 如需全部保留重复行，每个连接点都应用UNION ALL
   - 如部分需要去重，部分保留重复，可灵活混合使用
   - 最终效果取决于每个连接点的操作符选择

   **简言之：需要几个UNION ALL就写几个，每个连接点独立选择操作符。**



2. **查询合并后的列名**

   UNION结果集的列名由第一个SELECT语句决定。

   - 列名采用第一个SELECT语句中的列名或别名
   - 后续SELECT语句的列名会被忽略，==只需保证列的数量和数据类型兼容==
   - 建议为第一个SELECT的关键列定义明确的别名，以便结果集有清晰的列名

   ```MySQL
   SELECT prod_id AS 产品编号, prod_name AS 名称 FROM products
   UNION
   SELECT product_id, product_name FROM inventory
   ```

   结果集的列名将是"产品编号"和"名称"。

---

### 第十八章 全文本搜索 ###

#### **核心概念** ####

​	与通配符和正则表达式的模糊匹配不同，全文本搜索通过建立索引，可以高效地进行关键词搜索，并能根据匹配的良好程度进行结果排序。它不是所有引擎都支持，常用的`MyISAM`支持，而`InnoDB`在较新版本（5.6+）也开始支持。

#### **关键语法与操作** ####

- **启用全文本搜索支持**：在创建表时，使用`FULLTEXT`子句对指定列进行索引。 

  ```MySQL
  CREATE TABLE productnotes 
  (  
      ...  
   	note_text TEXT NOT NULL,  
      FULLTEXT(note_text) 
  ) ENGINE=MyISAM;
  ```

- **进行全文本搜索**：使用`Match()`指定被搜索的列，`Against()`指定搜索表达式。返回的结果自动按相关性高低（等级值）排序。 

  ```MySQL
  SELECT note_text FROM productnotes 
  WHERE Match(note_text) Against('rabbit');
  ```

- **使用查询扩展**：在`Against()`中使用`WITH QUERY EXPANSION`。首先进行基本搜索，然后MySQL检查匹配行并选择“有用”的词，最后再次进行搜索。这能找出可能相关但未直接包含关键词的结果，但也会返回更多不相关的结果。 

  ```MySQL
  SELECT note_text FROM productnotes 
  WHERE Match(note_text) Against('anvils' WITH QUERY EXPANSION);
  ```

- **布尔文本搜索**：在`Against()`中使用`IN BOOLEAN MODE`。它允许在不建立`FULLTEXT`索引的情况下进行全文本搜索（但很慢），并允许你定义复杂的搜索模式（必须包含、必须排除、等级控制等）。 操作符示例：`+`（必须存在），`-`（必须排除），`>`（增加等级值），`*`（词尾通配符）。 

  ```MySQL
  -- 搜索包含rabbit和bait的行 
  SELECT note_text FROM productnotes 
  WHERE Match(note_text) Against('+rabbit +bait' IN BOOLEAN MODE);
  ```

#### **易错点与技巧** ####

| 关键字/概念         | 说明/易错点                                                  |
| ------------------- | ------------------------------------------------------------ |
| **引擎支持**        | 在使用前，必须确认你的表使用了支持全文本搜索的存储引擎（如`MyISAM`）。 |
| **`Match()`中的列** | `Match()`中指定的列必须与`FULLTEXT`索引定义的列完全一致。    |
| **排序**            | 全文本搜索返回的结果默认按相关性排序。布尔方式（`IN BOOLEAN MODE`）不排序。 |
| **短词/内建非用词** | 索引时会忽略短词（默认3个及以下字符）和内建的非用词（如`the`, `and`等）。 |
| **50%规则**         | 如果一个词出现在50%以上的行中，则它被视为非用词而被忽略。此规则不适用于布尔方式。 |

#### 注意事项

1. **搜索引擎**

   **为何存在不同引擎？**

   ​	因为不同的应用场景对数据的需求不同，就像赛车、卡车、家用车对发动机的要求不同一样。MySQL设计成可插拔的存储引擎架构，就是为了让用户可以根据**速度、安全性、功能**这三个核心维度来选择最适合自己需求的“发动机”。

   **引擎之间的主要差别（以MyISAM和InnoDB为例）：**

   1. **核心目标不同：** 
      - **MyISAM（旧式代表）：** 设计目标是**极致的读取速度**。它像一辆轻量级的跑车，在单纯查询（SELECT）时非常快。但它不支持**事务**（无法保证一组操作要么全成功要么全失败）等高级功能，并且在频繁写入、系统崩溃时数据损坏的风险更高。
      -  **InnoDB（现代标准）：** 设计目标是**数据安全和支持高并发**。它像一辆坚固的豪华车，支持**事务**、**外键约束**和**行级锁**。这意味着它能更好地保证数据完整性，支持多人同时安全地读写数据，但因此在只读场景下可能比MyISAM稍慢。
   2. **功能支持不同（这就是全文搜索差异的原因）：** 全文搜索是一种需要特殊索引技术的复杂功能。在MySQL 5.6之前，**只有MyISAM引擎开发并内置了这个功能**，而InnoDB则没有。

   **重要更新：** 自 **MySQL 5.6 版本起，InnoDB 引擎也已经支持全文搜索**。现在通常推荐使用InnoDB，因为它具备了全文搜索功能，同时在其他方面（事务安全、并发控制）远超MyISAM。



2. **FULLTEXT**
   1. **核心含义：** 它是一种“倒排索引”。它不像普通索引那样直接索引整个字段值，而是**将文本内容（如文章、描述）拆分成一个个关键词（如“数据库”、“搜索”），并记录每个关键词出现在哪些数据行中**。这就像一本书末尾的“索引”页，能让你快速找到包含某个关键词的所有页面。
   2. **核心功能：** 
      - **高速搜索：** 与使用 `LIKE '%keyword%'`的逐行扫描相比，FULLTEXT 索引通过查询预建的关键词列表来定位数据，速度极快，尤其适合大文本字段。 
      - **相关性排序：** 它不仅能找到结果，还能根据关键词匹配的相关度（如出现频率、位置）对结果进行智能排序，将最相关的结果排在最前面，类似于搜索引擎的效果。 
      - **支持高级查询：** 支持“布尔模式”，可以进行更复杂的搜索，例如必须包含某个词、排除某个词，或者模糊匹配等。



3. **FULLTEXT指定列的数据索引、更新，以及时间复杂度**
   1. **“所有已有数据”指的是什么？** 指的是在创建 `FULLTEXT`索引**之前，已经存在于该表指定列中的所有文本数据**。它只针对你将要创建索引的那一列或几列，而不是表中所有列的数据。
   2. **稍后指定的格式还是 `FULLTEXT()`吗？** 不是。在创建表时，使用 `FULLTEXT (column_name)`作为表定义的一部分。如果在表创建后追加，则需要使用 `ALTER TABLE`语句，格式为：**`ALTER TABLE table_name ADD FULLTEXT (column_name);`**
   3. **为何要强调“索引所有数据”？稍后指定会不索引旧数据吗？** 强调“索引所有数据”是为了说明一个**关键特性**：无论何时创建索引，MySQL **都会**立即对表中该列**所有现有数据**进行一遍扫描和索引构建。这是为了确保索引的完整性。**不会**出现只索引新数据而忽略旧数据的情况。创建索引的操作是一个一次性、完整的构建过程。
   4. **`FULLTEXT`对指定列构建“关键词词典”，空间复杂度不会很庞杂吗？** 会占用额外空间，但它的“空间复杂度”通常是**高效且可接受的**。原因是： **倒排索引结构**：`FULLTEXT`索引（倒排索引）只存储关键词本身以及包含该关键词的行ID列表，而不是重复存储原始文本。 **分词优化**：它会对文本进行分词，忽略常见无意义词（如“的”、“a”、“the”），并通常统一转换为小写，这减少了存储量。 **权衡代价**：这种空间换来的的是查询性能的**巨大提升**，使得全文本搜索比使用 `LIKE '%keyword%'`的效率高几个数量级。对于需要全文搜索的场景，这个空间开销是必要且值得的。



4. **不具有词分隔符的语言不支持全文本搜索**

   **核心原因在于：MySQL的FULLTEXT索引依赖于“词”的概念，而它对“词”的定义基于空格分隔。**

   ​	对于英语等语言，句子由空格分隔的单词组成，例如“`This is a book`”，MySQL可以轻松地将其拆分为`This`、`is`、`a`、`book`等独立的词并建立索引。

   ​	但对于汉语“`这是一本书`”或日语“`これは本です`”，句子是连续的字符流，没有自然的空格分隔。早期的MySQL分词器无法智能地将它们正确切分为“`这`”、“`是`”、“`一本`”、“`书`”这样的有意义的词。它可能会错误地处理，例如按单个字符拆分或将整个句子当成一个“词”，导致搜索结果完全不准确或无法使用。

   **重要更新：** 现代MySQL版本（如5.7及以上）通过引入**ngram分词器** 已经很好地支持了中日韩文等语言，它通过滑动窗口将文本按固定长度（如2个字符“一书”、“书是”）进行切分，从而解决了这个问题。



5. **邻近搜索？**

   目前MySQL官方版本的原生全文本搜索功能**仍然不支持**邻近搜索（Proximity Search）。



6. **全文本搜索 VS 布尔模式**

   ​	它们更像是**目的不同的两种工具**，核心区别在于**是否进行智能的相关性排序**。以下是本质区别：

   1. **核心目标与结果排序（最根本区别）** **全文本搜索（自然语言模式）**：主要目标是**按相关性排序**。MySQL会计算每个结果的相关性分数（排名），将最匹配的排在最前面。**布尔模式**：主要目标是**进行复杂的、过滤式的匹配**。它关心的是“是或否”——某行数据是否满足你设定的复杂条件。它默认不按相关性排序，结果顺序可能是任意的。
   2. **功能与控制力** **全文本搜索**：语法简单，更“傻瓜化”，把排序工作交给MySQL。 **布尔模式**：功能强大，是“上位”体现在这里。它允许你使用`+`（必须包含）、`-`（必须排除）、`>`（提高排名）等操作符进行精细控制，实现自然语言模式做不到的复杂逻辑。
   3. **性能与细节** **布尔模式**有一个重要特点：它可能会**忽略频率过高**的词（如“the”、“and”），即使你没有明确排除它们。而自然语言模式会考虑所有词来计算相关性。

   **结论：它们不是替代关系，而是互补关系。**当你需要**“搜索得又好又准”**（如通用搜索引擎），用**自然语言模式**。当你需要**“精确过滤”**（如“找出包含‘错误’但排除‘警告’的日志”），用**布尔模式**。

---

### 第十九章 插入数据 ###

#### **核心概念** ####

`INSERT`语句用于向数据库表中插入新的行。

#### **关键语法与操作** ####

- **插入完整的行**：指定表名和要插入的值。**强烈建议**同时指定列名，这样可以避免因表结构变化（如列顺序改变）而导致的错误。 

  ```MySQL
  -- 不安全，依赖列的定义顺序 
  INSERT INTO customers VALUES(NULL, 'Pep E. LaPew', ...); 
  -- 安全，明确指定列 
  INSERT INTO customers(cust_name, cust_address, ...) 
  VALUES('Pep E. LaPew', '100 Main Street', ...);
  ```

  **插入多个行**：使用多条`INSERT`语句，或者用一条`INSERT`语句，每组值用逗号分隔。后者性能通常更好。 

  ```MySQL
  -- 方式一：多条语句 
  INSERT INTO customers(...) VALUES(...); INSERT INTO customers(...) VALUES(...); 
  -- 方式二：单条语句（推荐） 
  INSERT INTO customers(...) VALUES(...),      (...);
  ```

- **插入检索出的数据**：使用`INSERT SELECT`将一条`SELECT`语句的结果插入到表中。这是数据迁移和复制的强大工具。

  ```MySQL
  INSERT INTO customers(cust_id, cust_name, ...) 
  SELECT cust_id, cust_name, ... FROM custnew;
  ```

#### **易错点与技巧** ####

| 关键字/概念                 | 说明/易错点                                                  |
| --------------------------- | ------------------------------------------------------------ |
| **省略列**                  | 如果某列定义中允许`NULL`值或有默认值，可以在`INSERT`语句中省略该列。 |
| **省略值**                  | 如果某列不允许`NULL`且没有默认值，省略它会导致错误。         |
| **提高整体性能**            | 通过使用`INSERT LOW_PRIORITY INTO`，可以降低`INSERT`语句的优先级，适用于需要优先处理`SELECT`的场景。 |
| **`INSERT SELECT`中的列序** | `SELECT`中的列与`INSERT`语句中==指定的列必须按位置对应，列名不必相同==。 |

---

### 第二十章 更新和删除数据 ###

#### **核心概念** ####

- `UPDATE`：用于更新表中**已存在**的行。
- `DELETE`：用于从表中**删除**行。

**重要警告**：使用这两条语句时，**永远不要省略`WHERE`子句**，除非你确实想更新或删除表中的每一行。

#### **关键语法与操作** ####

- **更新数据**：

  - **更新特定行**：使用`UPDATE...SET`指定要更新的表和列的新值，用`WHERE`子句过滤要更新的行。 

    ```MySQL
    UPDATE customers SET cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;
    ```

  - **更新多个列**：用逗号分隔“列=值”对。 

    ```MySQL
    UPDATE customers SET cust_name = 'The Fudds',    cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;
    ```

  - **删除某个列的值**：可将其更新为`NULL`（前提是该列允许`NULL`）。 

    ```MySQL
    UPDATE customers SET cust_email = NULL WHERE cust_id = 10005;
    ```

- **删除数据**： 

  - **删除特定行**：使用`DELETE FROM`指定表，用`WHERE`子句过滤要删除的行。

    ```MySQL
     DELETE FROM customers WHERE cust_id = 10006;
    ```

  - **删除所有行**：省略`WHERE`子句会清空整个表（但更快的操作是`TRUNCATE TABLE`）。 

    ```MySQL
    DELETE FROM customers; -- 清空customers表
    ```

#### **易错点与指导原则** ####

| 操作                    | 关键点/指导原则                                              |
| ----------------------- | ------------------------------------------------------------ |
| **`UPDATE`/`DELETE`前** | 1. **务必使用`WHERE`子句**，除非意图操作所有行。 2. 先用`SELECT`测试`WHERE`子句，确保过滤条件正确。 3. **MySQL没有撤销（undo）功能**，操作前务必谨慎。 |
| **`UPDATE`特性**        | 可以使用子查询来更新数据。                                   |
| **`IGNORE`关键字**      | 使用`UPDATE IGNORE`，即使更新多行时发生部分错误，也会继续执行。 |
| **`DELETE`特性**        | `DELETE`删除的是整行，不是列。要删除特定列，请使用`UPDATE`将其设为`NULL`。 |
| **外键与引用完整性**    | 如果表有关联数据（如外键约束），可能无法直接删除或更新，除非设置了级联操作。 |

#### 注意事项

1. **UPDATE与DELETE**

   UPDATE 和 DELETE 的核心区别在于其操作对象和结果：

   1. **功能目的**： **UPDATE** 用于**修改**现有行中一列或多列的**值**。它改变数据的内容。 **DELETE** 用于从表中**移除**（删除）整个**行**。它减少数据的数量。
   2. **操作单位**： UPDATE 操作的是行内的**列**，使用 `SET`子句指定要修改的列和新值。 DELETE 操作的是整个**行**，不针对特定列。

   **关键共同点**：两者都必须谨慎使用 `WHERE`子句来精确指定目标，否则会对表中所有数据产生影响。

---

### 第二十一章 创建和操纵表 ###

#### **核心概念** ####

本章讲解如何使用 SQL 语句直接定义、修改和删除数据库中的表结构。这是管理数据库模式（schema）的基础。

#### **关键语法与操作** ####

- **创建表**：使用 `CREATE TABLE`语句，必须指定表名和列定义。
  
  ```MySQL
   CREATE TABLE customers (
       cust_id      int       NOT NULL AUTO_INCREMENT,  
   	 cust_name    char(50)  NOT NULL,  
       cust_address char(50)  NULL,  
       PRIMARY KEY (cust_id) 
   ) ENGINE=InnoDB;
  ```
  
- **指定列定义**：每个列定义包括列名、数据类型，并可包含**NULL/NOT NULL**约束和**默认值**。 **`NULL`与 `NOT NULL`**：规定列是否允许为空值。未指定时默认为 `NULL`。 **`DEFAULT`**：指定插入行时未给出值的列的默认值。

  ```MySQL
  quantity int NOT NULL DEFAULT 1,
  ```

- **主键**：使用 `PRIMARY KEY`关键字定义，确保行的唯一性。可以是单个列或多列（复合主键）。 主键值必须唯一且不能为 `NULL`。 
  
  ```MySQL
  PRIMARY KEY (order_num, order_item) -- 复合主键
  ```
- **AUTO_INCREMENT**：使列在插入新行时自动生成一个唯一的增量值。每个表只能有一个 `AUTO_INCREMENT`列，它必须被索引（通常作为主键）。 
  
  ```MySQL
  cust_id int NOT NULL AUTO_INCREMENT,
  ```
- **指定存储引擎**：使用 `ENGINE=`子句。不同的引擎具有不同的特性（如事务支持、全文搜索等）。
| 引擎       | 特点                                                   | 常见用途                             |
| ---------- | ------------------------------------------------------ | ------------------------------------ |
| **InnoDB** | 支持事务、外键、行级锁。不支持全文搜索（MySQL 5.5-）。 | 需要事务安全、高可靠性的应用。       |
| **MyISAM** | 不支持事务、外键。支持全文搜索、表级锁。性能极高。     | 只读或读多写少、需要全文搜索的应用。 |
| **MEMORY** | 数据存储在内存中，速度极快。服务器重启后数据丢失。     | 临时表、高速缓存。                   |

- **更新表结构**：使用 `ALTER TABLE`语句。**警告**：理想情况下，表创建后应避免频繁更改。
  - **添加列**：`ADD` 	`ALTER TABLE vendors ADD vend_phone CHAR(20);`
  
  - **删除列**：`DROP COLUMN`      `ALTER TABLE vendors DROP COLUMN vend_phone;`

  - **定义外键**（为现有表）： 
  
    ```mysql
    ALTER TABLE orderitems ADD CONSTRAINT fk_orderitems_orders 
    FOREIGN KEY (order_num) REFERENCES orders (order_num);
    ```
  
- **删除表**：使用 `DROP TABLE`语句。**此操作不可撤销！** `DROP TABLE customers2;`

- **重命名表**：使用 `RENAME TABLE`语句。可以一次重命名多个表。 `RENAME TABLE customers2 TO customers; RENAME TABLE a TO b, c TO d, e TO f;`

#### **易错点与技巧** ####

| 操作/概念                | 说明/易错点                                                  |
| ------------------------ | ------------------------------------------------------------ |
| **`IF NOT EXISTS`**      | 在 `CREATE TABLE`中使用可防止因表已存在而报错。              |
| **字符串与 `NULL`**      | 空字符串(`''`)是一个有效值，不是 `NULL`。                    |
| **`AUTO_INCREMENT`覆盖** | 可以在 `INSERT`中为 `AUTO_INCREMENT`列指定一个唯一值，后续增量将从此值开始。 |
| **`ALTER TABLE`风险**    | 在更改表结构前，**务必备份数据**。复杂的结构更改可能需要手动创建新表并迁移数据。 |
| **外键与引擎**           | 外键不能跨不同存储引擎的表。                                 |

#### 注意事项

1. **搜索引擎的指定**

   MySQL的存储引擎主要在**创建表时**使用 `CREATE TABLE`语句通过 `ENGINE`关键字指定。例如：`CREATE TABLE mytable (...) ENGINE=InnoDB;`。此外，也可以在**修改表结构时**使用 `ALTER TABLE`语句进行更改，例如：`ALTER TABLE mytable ENGINE=MyISAM;`。如果建表时未明确指定，MySQL将使用服务器配置的**默认存储引擎**（通常为InnoDB）。



2. **避免表名重复**

   在 `CREATE TABLE`语句中使用 `IF NOT EXISTS`子句。其书写格式如下：

   ```MySQL
   CREATE TABLE IF NOT EXISTS 
   table_name (
     column1 datatype,
     column2 datatype,
     ...
   );
   ```

   当指定此子句后，如果目标数据库中已存在同名表，MySQL 不会报错，而是抛出一个警告并跳过此表的创建操作。这是最常用且推荐的避免重复创建表的方法。



3. **指定为NULL vs DEFAULT NULL**

   ​	两者不完全等同，但效果通常一致。

   ​	指定 `NULL`表示该列允许存储 `NULL`值，这是一个约束。而 `DEFAULT NULL`是为该列设置默认值为 `NULL`。如果只指定 `NULL`而未用 `DEFAULT`明确设置默认值，MySQL 会隐式地将其默认值设为 `NULL`。因此，在大多数情况下，最终效果是相同的。



4. **空串 vs NULL**

   好的，这是一个非常核心的概念。

   **空串的含义和作用**

   空串是一个**长度为0的字符串值**（`''`），它是一个有效的、已知的数据，其含义通常被定义为“空白”、“无内容”或“未填写”。它必须存储在具有字符类型（如 `CHAR`, `VARCHAR`, `TEXT`）的列中。

   **NULL的含义**

   ​	`NULL`表示“未知”或“缺失值”。它不是一个值，而是一个状态，意味着“此处没有输入任何数据（包括空串）”。

   **核心区别**

   1. **本质不同**：空串是一个具体的**值**；`NULL`表示**值的缺失状态**。
   2. **数据比较**：`'' = ''`的结果是 True（相等），但 `NULL = NULL`的结果是 `NULL`（未知）。判断是否为 `NULL`必须使用 `IS NULL`或 `IS NOT NULL`。
   3. **在查询中的体现**：查询 `WHERE column = ''`只会返回空串的行，而不会返回 `NULL`的行。反之亦然。

   **一个形象的例子**：

   在 `email`字段中：

   - 存储 `NULL`表示“不知道用户的邮箱是什么”。
   - 存储空串 `''`表示“知道用户没有邮箱”。



5. **UPDATE与DROP对列的删除**

   ​	UPDATE和DROP删除列有本质区别：

   ​	**UPDATE** 是**数据操作**，用于将列的值更新为NULL（需列允许NULL），不改变表结构。列依然存在，只是内容被清空。格式：`UPDATE table SET column = NULL;`

   ​	**DROP** 是**结构修改**，使用`ALTER TABLE`永久删除整个列及其所有数据，会改变表结构。列被完全移除。格式：`ALTER TABLE table DROP COLUMN column;`

   **核心区别**：UPDATE清空列内容，列仍存在；DROP直接删除列本身，列不复存在。

---

### 第二十二章 使用视图 ###

#### **核心概念** ####

​	视图是虚拟的表。它只包含一个动态检索数据的查询，而不包含任何数据本身。使用视图可以简化复杂查询、隐藏数据复杂性、重用SQL逻辑和授予部分数据访问权限。

#### **关键语法与操作** ####

- **创建视图**：使用 `CREATE VIEW ... AS SELECT ...`语句。 

  ```mysql
  CREATE VIEW productcustomers AS SELECT cust_name, cust_contact, prod_id FROM customers, orders, orderitems WHERE customers.cust_id = orders.cust_id AND orderitems.order_num = orders.order_num;
  ```

- **使用视图**：像使用普通表一样在 `SELECT`语句中使用视图名。 
  
  ```mysql
  SELECT cust_name, cust_contact FROM productcustomers WHERE prod_id = 'TNT2';
  ```
  
- **删除视图**：使用 `DROP VIEW`语句。 `DROP VIEW productcustomers;`

- **更新视图**：可以先 `DROP`再 `CREATE`，或使用 `CREATE OR REPLACE VIEW`。

#### **视图的应用场景** ####

- **简化复杂的联结**：将复杂的多表联结查询封装在视图中，使后续查询变得简单。

- **重新格式化检索出的数据**：在视图中使用计算字段、函数，使结果更易于使用。

  ```mysql
  CREATE VIEW vendorlocations AS SELECT Concat(vend_name, ' (', vend_country, ')') AS vend_title FROM vendors;
  ```

- **过滤不想要的数据**：在视图查询中使用 `WHERE`子句，过滤掉不符合条件的数据。 
  ```mysql
  CREATE VIEW customeremaillist AS SELECT cust_id, cust_name, cust_email FROM customers WHERE cust_email IS NOT NULL;
  ```
- **使用计算字段**：简化包含计算字段的查询。 
  
  ```mysql
  CREATE VIEW orderitemsexpanded AS SELECT order_num, prod_id, quantity, item_price, quantity*item_price AS expanded_price FROM orderitems;
  ```
#### **视图的规则与限制** ####

- **视图是否可更新？** 并非所有视图都允许执行 `INSERT`, `UPDATE`, `DELETE`操作。如果视图定义中包含以下内容，则通常**不可更新**：

| 视图定义中包含                                | 示例                                                    | 是否可更新 |
| --------------------------------------------- | ------------------------------------------------------- | ---------- |
| **分组（`GROUP BY`， `HAVING`）**             | `SELECT cust_id, COUNT(*) FROM orders GROUP BY cust_id` | 否         |
| **联结**                                      | `SELECT ... FROM a JOIN b ...`                          | 通常否     |
| **子查询**                                    | `SELECT ... WHERE col IN (SELECT ...)`                  | 通常否     |
| **聚集函数（`Min()`, `Count()`, `Sum()`等）** | `SELECT AVG(column) ...`                                | 否         |
| **`DISTINCT`**                                | `SELECT DISTINCT column ...`                            | 否         |
| **导出（计算）列**                            | `SELECT quantity*price ...`                             | 否         |

- **其他限制**： 视图必须唯一命名。 可以创建嵌套视图。 `ORDER BY`可以在视图中使用，但如果从视图检索的 `SELECT`中也包含 `ORDER BY`，则视图中的 `ORDER BY`会被覆盖。 视图不能索引，也不能有关联的触发器或默认值。

#### **易错点与技巧** ####

| 操作/概念                    | 说明/易错点                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| **性能问题**                 | 视图本身不包含数据，每次使用视图时都会执行其背后的查询。复杂的视图或多重嵌套可能影响性能。 |
| **`CREATE OR REPLACE VIEW`** | 更安全的更新视图方式，避免因视图不存在而报错。               |
| **保护底层表结构**           | 使用视图可以屏蔽底层表的列名变化。如果表结构改变，只需修改视图定义即可使依赖视图的查询继续工作。 |
| **安全性**                   | 可以通过视图授予用户对特定行或列的访问权限，而不直接授予基表的权限。 |

#### 注意事项

1. **视图不能被更新：缺陷？**

   ​	视图更新的限制并非设计缺陷，而是源于其**虚拟表的本质**。视图本身不存储数据，其内容动态生成于基础表。

   1. **数据映射模糊**：若视图由多表联结（JOIN）定义，数据库引擎无法明确应将`UPDATE`或`INSERT`操作映射到哪个基础表的哪一行，可能破坏数据完整性。
   2. **聚合结果不可逆**：包含`GROUP BY`、聚合函数或`DISTINCT`的视图，其数据是派生结果。例如，无法直接“更新”一个平均值，因为它由多行数据计算得出。
   3. **确保数据一致性**：限制更新是为了防止对非直接映射的派生数据（如计算列、子查询结果）进行修改，避免产生逻辑上矛盾或不确定的更新操作。

   ​        这看似是限制，实则是**维护数据一致性和逻辑正确性的关键机制**。如需通过视图修改数据，应确保其定义足够简单（例如，仅来自单表且包含所有必要约束），或转而操作其基础表。

---

### 第二十三章 使用存储过程

#### **核心概念**

- **存储过程**：Stored Procedure，是为以后使用而保存的一条或多条 SQL 语句的集合。可将其视为批处理文件，但其作用不仅限于批处理。
- **优点**： 
  - **简化复杂操作**：通过封装处理，简化复杂的操作。 
  - **保证数据完整性**：确保所有开发人员和应用程序使用相同的代码。 
  - **简化变动管理**：如果表名、列名或业务逻辑有变化，只需更改存储过程的代码。 
  - **提高性能**：通常比执行单独的 SQL 语句更快。

- **缺点**：编写比基本 SQL 语句复杂，可能需要更高的技能和安全访问权限。

#### **关键语法与操作**

- **执行存储过程**：使用 `CALL`语句。存储过程可以接受参数。 

  ```mysql
  CALL productpricing(@pricelow, @pricehigh, @priceaverage);
  ```

- **创建存储过程**：使用 `CREATE PROCEDURE`语句。如果存储过程接受参数，需在 `()`中列出。使用 `BEGIN ... END`语句来限定存储过程体。 
  
  ```mysql
  CREATE PROCEDURE productpricing() 
  BEGIN   
  	SELECT Avg(prod_price) AS priceaverage   
  	FROM products; 
  END;
  ```
  
- **删除存储过程**：使用 `DROP PROCEDURE`语句。 
  
  ```mysql
  DROP PROCEDURE productpricing;
  ```
  
- **使用参数**：存储过程的参数有三种类型：

  -  `IN`（传入存储过程） 

  - `OUT`（从存储过程传出）

  -  `INOUT`（对存储过程传入和传出）
  ```mysql
  CREATE PROCEDURE productpricing
  (    
      OUT pl DECIMAL(8,2),    
      OUT ph DECIMAL(8,2),    
      OUT pa DECIMAL(8,2) 
  ) 
  BEGIN    
  	SELECT Min(prod_price) INTO pl FROM products;    
  	SELECT Max(prod_price) INTO ph FROM products;    
  	SELECT Avg(prod_price) INTO pa FROM products; 
  END;
  ```
    调用带 `OUT`参数的存储过程时，必须传递变量名（以 `@`开头）。使用 `SELECT`查看变量值。 
  
  ```mysql
  CALL productpricing(@pricelow, @pricehigh, @priceaverage); 
  SELECT @priceaverage; 
  SELECT @pricehigh, @pricelow, @priceaverage;
  ```
- **使用变量与逻辑控制**：使用 `DECLARE`定义局部变量，使用 `IF`等语句进行逻辑控制。

   ```mysql
  CREATE PROCEDURE ordertotal
  (    IN onumber INT,    IN taxable BOOLEAN,    OUT ototal DECIMAL(8,2) ) 
  COMMENT 'Obtain order total, optionally adding tax' 
  BEGIN    
      -- Declare variable for total    
      DECLARE total DECIMAL(8,2);    
      
      -- Declare tax percentage    
      DECLARE taxrate INT DEFAULT 6;    
      
      -- Get the order total    
      SELECT Sum(item_price*quantity) FROM orderitems    
      WHERE order_num = onumber INTO total;   
      
      -- Is this taxable?    
      IF taxable THEN        
      	-- Yes, so add taxrate to the total        
      	SELECT total+(total/100*taxrate) INTO total;    
      END IF;    
      
      -- And finally, save to out variable    
      SELECT total INTO ototal; 
      
  END;
  ```
- **检查存储过程**： 

  - 显示创建语句：`SHOW CREATE PROCEDURE ordertotal;` 
  - 显示状态列表：`SHOW PROCEDURE STATUS LIKE 'ordertotal';`

#### **易错点与技巧**

| 操作/概念        | 说明/易错点                                                  |
| ---------------- | ------------------------------------------------------------ |
| **命令行分隔符** | 在 `mysql`命令行中，默认分隔符 `;`会与存储过程中的 `;`冲突。需要使用 `DELIMITER //`临时更改语句分隔符，定义完存储过程后再改回 `DELIMITER ;`。 |
| **参数数据类型** | 存储过程的参数允许的数据类型与表中使用的数据类型相同。记录集不是允许的类型。 |
| **`INTO`位置**   | 用于将检索值保存到变量（或参数）的 `INTO`子句应位于 `FROM`子句之前。 |
| **`COMMENT`**    | 存储过程的可选注释，会在 `SHOW PROCEDURE STATUS`的结果中显示。 |
| **智能存储过程** | 将业务规则和智能处理放入存储过程，其威力才真正显现。         |

#### 注意事项

1. **批文件**

   批处理文件（**B**atch File，扩展名为`.bat`或`.cmd`）是一个包含一系列**DOS命令**的纯文本文件（**P**lain Text File）。当运行此文件时，系统会按顺序自动执行其中的所有命令，从而将重复操作自动化（**A**utomate Repetitive Tasks）。

   它的核心作用是简化日常或重复性任务，例如批量创建文件夹或自动运行多个程序。常用命令包括`echo`（显示信息）、`pause`（暂停）和`call`（调用其他批处理文件）。你还可以使用参数（例如`%1`, `%2`）使脚本更具灵活性。



2. **存储过程返回什么？**

   **是的，MySQL存储过程可以返回值和结果集，但方式不同：**

   **1. 返回值（单个值）**

   - 使用 `OUT`参数：在存储过程中定义 `OUT`参数，用 `INTO`将查询结果赋给该参数

   - 调用后通过会话变量获取：

     ```MySQL
     CALL proc_name(@output); SELECT @output;
     ```

   **2. 返回结果集（多行数据）**

   - 直接在存储过程中执行 `SELECT`语句
   - 调用时会自动返回结果集：`CALL proc_name();`

   **同时获取两者**：在同一个存储过程中既包含返回结果集的 `SELECT`语句，又设置 `OUT`参数值。调用时需要分别处理 - 先获取结果集，再通过会话变量读取输出值。

   **关键区别**：结果集是直接返回的查询结果，而值需要通过参数机制传递。

---

### 第二十四章：使用游标

#### **核心概念**

- **游标**：Cursor，是一个存储在 MySQL 服务器上的数据库查询结果集。

  ​		它不是一条 `SELECT`语句，而是被该语句检索出来的结果集。

- **用途**：在检索出来的行中前进或后退一行或多行。主要用于**交互式应用**，用户需要滚动屏幕上的数据。

- **重要限制**：MySQL 游标**只能用于存储过程（和函数）**。

#### **关键语法与操作**

使用游标涉及几个明确的步骤：

1. **声明（定义）游标**：使用 `DECLARE`语句。这个过程==实际上没有检索数据==。
2. **打开游标**：使用 `OPEN`语句。这个过程用前面定义的 `SELECT`语句把数据实际检索出来。
3. **取出数据**：使用 `FETCH`语句。根据需要取出（检索）游标中的各行。
4. **关闭游标**：使用 `CLOSE`语句。

- **创建（声明）游标**：使用 `DECLARE cursor_name CURSOR FOR select_statement;`。此语句必须位于变量和条件定义之后。

  ```mysql
  CREATE PROCEDURE processorders() 
  BEGIN    
  	DECLARE ordernumbers CURSOR    
  	FOR SELECT order_num FROM orders; 
  END;
  ```

- **打开和关闭游标**：

  -  `OPEN ordernumbers;`-- 处理 `OPEN`语句时执行查询，存储检索出的数据以供浏览和滚动。
  -  `CLOSE ordernumbers;`-- 游标关闭后，如果没有重新打开，则不能使用。`CLOSE`释放游标使用的所有内部内存和资源。

- **使用游标数据**：使用 `FETCH`语句访问游标中的一行，并向前移动游标内部的行指针。 

  - **基本使用**：`FETCH ordernumbers INTO o;`-- 将当前行的数据检索到局部变量 `o`中。 
  - **循环检索所有行**：通常需要与循环和条件处理一起使用。 
  ```mysql
  CREATE PROCEDURE processorders() 
  BEGIN    
  	-- Declare local variables    
  	DECLARE done BOOLEAN DEFAULT 0;    
  	DECLARE o INT;    
  	-- Declare the cursor    
  	DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;    
  	-- Declare continue handler    
  	DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;    
  	-- Open the cursor    
  	OPEN ordernumbers;    
  	-- Loop through all rows    
  	REPEAT        
  		-- Get order number        
  		FETCH ordernumbers INTO o;   
          -- End of loop    
          UNTIL done END REPEAT;    
      -- Close the cursor   
      CLOSE ordernumbers; 
  END;
  ```
  - **`CONTINUE HANDLER`**：条件出现时被执行的代码。`SQLSTATE '02000'`是一个“未找到”条件，当 `REPEAT`由于没有更多的行供循环而不能继续时，出现这个条件。它在这里将 `done`设置为 1（真）。

- **综合示例**：一个更完整的例子，对检索出的每个订单号调用另一个存储过程来计算总价，并将结果存入新表。 

    ```mysql
    CREATE PROCEDURE processorders() 
    BEGIN    
    	DECLARE done BOOLEAN DEFAULT 0;   
        DECLARE o INT;   
        DECLARE t DECIMAL(8,2); 
        DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;  
        DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done=1;  
        CREATE TABLE IF NOT EXISTS ordertotals (order_num INT, total DECIMAL(8,2)); 
        OPEN ordernumbers;  
        REPEAT      
            FETCH ordernumbers INTO o;     
            CALL ordertotal(o, 1, t);     
            INSERT INTO ordertotals(order_num, total) VALUES(o, t);  
        UNTIL done END REPEAT;   
        CLOSE ordernumbers; 
    END;
    ```

#### **易错点与技巧**

| 操作/概念             | 说明/易错点                                                  |
| --------------------- | ------------------------------------------------------------ |
| **`DECLARE`语句顺序** | 在存储过程中，`DECLARE`语句的次序有严格要求：局部变量必须在任意游标或句柄之前定义，而句柄必须在游标之后定义。 |
| **`FETCH`不返回数据** | 当 `FETCH`没有取到数据时（如已到结果集末尾），不会报错，而是会设置 `SQLSTATE`为 `02000`（`NOT FOUND`）。必须通过句柄来捕获这种情况。 |
| **游标性能**          | 游标可能比面向集合的操作慢，因为它们逐行操作。应尽量避免在需要高性能的代码中使用。 |
| **只读**              | MySQL 游标是只读的，不能通过游标更新数据。                   |
| **`REPEAT`vs `LOOP`** | 本例使用 `REPEAT`，但 MySQL 也支持 `WHILE`和 `LOOP`循环。`REPEAT`的语法使其特别适合对游标进行循环。 |

#### 注意事项

1. **关于`DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done=1; `**

   **逐词语解读：**

   - `DECLARE`：声明一个对象（此处为处理器）
   - `CONTINUE HANDLER`：定义当特定条件发生时，**继续执行**后续语句的处理器
   - `FOR SQLSTATE '02000'`：触发条件为SQL状态码‘02000’（代表“无更多数据”，常见于游标遍历结束）
   - `SET done=1;`：触发时将变量`done`设为1，通常用于循环退出判断。

   **能否改用EXIT？可以。** 

   若改为`EXIT HANDLER`，则触发后会**立即退出**当前的`BEGIN...END`代码块（例如存储过程主体）。

---

### 第二十五章：使用触发器

#### **核心概念**

- **触发器**：Trigger，是数据库中的一种特殊对象，它是与表事件（INSERT, UPDATE, DELETE）相关的已命名的数据库对象，当表上发生特定事件时，将激活该对象。
- **用途**：触发器可用于数据验证、审计、维护衍生数据（如摘要表）等。它们允许在数据库层面执行业务规则，而不是在应用程序代码中。
- **关键点**： 触发器是自动执行的。 与表相关联，并在特定事件发生时激活。 可以设置为在事件发生前（BEFORE）或发生后（AFTER）执行。

#### **关键语法与操作**

- **创建触发器**：使用 `CREATE TRIGGER`语句。必须指定触发器名称、关联的表、触发事件和时机。 

  ```mysql
  CREATE TRIGGER trigger_name 
  {BEFORE | AFTER} 
  {INSERT | UPDATE | DELETE} 
  ON table_name 
  FOR EACH ROW trigger_body;
  ```
  
- **触发器时机（Timing）**： **`BEFORE`**：在触发事件（INSERT, UPDATE, DELETE）**之前**执行触发器。常用于数据验证和净化。 **`AFTER`**：在触发事件**之后**执行触发器。常用于审计、日志记录或更新其他表。

- **`FOR EACH ROW`**：MySQL 目前只支持行级触发器，即受事件影响的每一行都会激活一次触发器。此子句是必须的。

- **访问新旧数据**：在触发器体内，使用 `NEW`和 `OLD`虚拟表来访问受影响行的数据。 **`INSERT`触发器**：可以访问 `NEW.column_name`来获取被插入的新值。`OLD`不可用。 **`UPDATE`触发器**：可以访问 `OLD.column_name`来获取更新前的值，访问 `NEW.column_name`来获取更新后的值。 **`DELETE`触发器**：可以访问 `OLD.column_name`来获取被删除的值。`NEW`不可用。

- **`INSERT`触发器示例**：在插入新订单后，自动返回新的订单号。 
  
  ```mysql
  CREATE TRIGGER neworder 
  AFTER INSERT ON orders 
  FOR EACH ROW SELECT NEW.order_num;
  ```
  
- **`DELETE`触发器示例**：在删除一个订单前，将其存档。 

    ```mysql
    CREATE TRIGGER deleteorder 
    BEFORE DELETE ON orders 
    FOR EACH ROW 
    BEGIN    
    	INSERT INTO archive_orders(order_num, order_date, cust_id)    
    	VALUES(OLD.order_num, OLD.order_date, OLD.cust_id); 
    END;
    ```

- **`UPDATE`触发器示例**：在更新供应商州名前，确保其总是大写。 

    ```mysql
    CREATE TRIGGER updatevendor 
    BEFORE UPDATE ON vendors 
    FOR EACH ROW 
    SET NEW.vend_state = UPPER(NEW.vend_state);
    ```

- **删除触发器**：使用 `DROP TRIGGER`语句。 

  ```mysql
  DROP TRIGGER trigger_name;
  ```

#### **易错点与技巧**

| 操作/概念                              | 说明/易错点                                                  |
| -------------------------------------- | ------------------------------------------------------------ |
| **每个表每个事件每次只允许一个触发器** | 一个表对每个事件（INSERT, UPDATE, DELETE）和每个时机（BEFORE, AFTER）只能有一个触发器。例如，一个表不能有两个 `BEFORE UPDATE`触发器。 |
| **`BEFORE`触发器的失败**               | 如果 `BEFORE`触发器执行失败，则相应的 SQL 语句（INSERT/UPDATE/DELETE）也不会执行。 |
| **`AFTER`触发器的失败**                | 如果 `AFTER`触发器（或语句本身）失败，`BEFORE`触发器所做的任何更改也会被回滚。 |
| **多语句触发器**                       | 如果触发器体包含多条语句，需要使用 `BEGIN ... END;`块。在命令行中使用时，记得用 `DELIMITER`临时更改语句分隔符。 |
| **`NEW`值的修改**                      | 在 `BEFORE`触发器中，可以修改 `NEW`的值（例如，用 `SET NEW.column = value;`），从而改变实际插入或更新的数据。在 `AFTER`触发器中，`NEW`是只读的。 |
| **事务表**                             | 如果表是事务表（如 InnoDB），触发器中的语句与触发语句在同一个事务中，可以一起被回滚。 |
| **性能**                               | 触发器是自动执行的，对性能有直接影响。复杂的触发器会降低数据操作的速度。 |

#### 注意事项

1. **触发器命名**

   命名限制可以归纳为以下几点：

   1. **唯一性范围**：触发器名称在**单个数据库内**必须是唯一的，但不同数据库中的触发器可以重名。更重要的是，**同一个表上的触发器必须拥有不同的名称**，即使它们被不同的事件（INSERT/UPDATE/DELETE）或时机（BEFORE/AFTER）激活。
   2. **命名规则**：名称最大长度为64个字符，遵循MySQL标识符的通用规则（例如，可以包含数字、字母、`$`、`_`等，但建议使用有意义的、简洁的英文或下划线组合）。
   3. **大小写敏感性**：在Unix/Linux系统上，触发器名称是大小写敏感的；在Windows系统上则不敏感。为保证可移植性，建议始终使用小写。

   简而言之，最关键的限制是：**触发器名在其所属的表内必须唯一**。



2. **触发器对象**

   触发器的触发对象是**表**，并且与作用在该表上的特定**数据变动语句**（即DML事件：`INSERT`、`UPDATE`、`DELETE`）绑定，不能任意指定。

   在创建触发器时，你必须明确指定一个表以及一个（且仅一个）上述事件。例如，你可以创建一个在`employees`表发生`UPDATE`**之前**触发的触发器。



3. **虚拟表 NEW 与 OLD**

   ​	虚拟表是MySQL在触发器执行过程中自动创建、存储在内存中的临时数据表，用于代表被操作行的新旧数据状态。**NEW和OLD是MySQL严格限定的关键字**，不可自拟。它们有特定含义且仅在特定触发器中可用：

   - **NEW**：代表`INSERT`操作中新插入的行，或`UPDATE`操作中更新后的新行。可修改其值（在`BEFORE`触发器中）。
   - **OLD**：代表`DELETE`操作中要删除的行，或`UPDATE`操作中更新前的旧行。其值为只读，不可修改。



4. **NEW OLD 对于三种语句的 BEFORE AFTER 情况的可读性、可更新性**

   | 触发事件   | 触发器时机 | NEW 虚拟表                                   | OLD 虚拟表         | 说明与常见用途                                               |
   | ---------- | ---------- | -------------------------------------------- | ------------------ | ------------------------------------------------------------ |
   | **INSERT** | **BEFORE** | **可读、可更新**  （修改后值将被插入）       | **不可用**         | 数据验证、业务规则检查、数据格式化（如：自动生成ID、格式化电话号码）。 |
   |            | **AFTER**  | **可读、不可更新**  （插入已完成，值为只读） | **不可用**         | 数据插入后的操作，如审计日志、触发其他业务逻辑。             |
   | **UPDATE** | **BEFORE** | **可读、可更新**  （修改后值将被写入）       | **可读、不可更新** | 数据验证、计算派生字段（如：自动更新`last_updated`时间戳）。 |
   |            | **AFTER**  | **可读、不可更新**                           | **可读、不可更新** | 数据变更审计、记录历史快照、同步更新其他表。                 |
   | **DELETE** | **BEFORE** | **不可用**                                   | **可读、不可更新** | 删除前的检查（如：确保引用完整性）、归档被删除的数据。       |
   |            | **AFTER**  | **不可用**                                   | **可读、不可更新** | 删除后的清理操作、记录删除事件。                             |



5. **触发器调用存储过程？**

   ​	不，目前MySQL的触发器**不支持**直接调用存储过程。这是一个明确的限制：在触发器体内不能使用`CALL`语句来执行存储过程。如果需要实现复杂逻辑，必须将存储过程中的代码直接写入触发器定义中，或者通过应用程序层来协调调用。

   ​	这个限制是MySQL与其他一些数据库管理系统（如SQL Server）在功能上的一个差异。



6. **触发器的“拦截”功能**

   **最佳方式：使用SIGNAL语句（MySQL 5.5+）**

   在BEFORE触发器中，通过条件判断后使用SIGNAL抛出自定义错误：
   
   ```MySQL
   IF NEW.column_name = 'invalid_value' THEN
       SIGNAL SQLSTATE '45000' 
       SET MESSAGE_TEXT = '拒绝插入：检测到无效值';
   END IF;
   ```

---

### 第二十六章 管理事务处理

#### **核心概念**

- **事务处理**：Transaction Processing，用于管理必须成批执行的 SQL 操作，以保证数据库的完整性。事务内的语句要么全部成功，要么全部失败。
- **事务的 ACID 属性**： **原子性（Atomicity）**：事务是一个不可分割的工作单元。 **一致性（Consistency）**：事务使数据库从一个一致状态转变为另一个一致状态。 **隔离性（Isolation）**：一个事务的执行不能被其他事务干扰。 **持久性（Durability）**：事务一旦提交，其对数据的改变是永久性的。
- **关键术语**： **事务（Transaction）**：一组 SQL 语句。 **回退（Rollback）**：撤销指定 SQL 语句的过程。 **提交（Commit）**：将未存储的 SQL 语句结果写入数据库表。 **保留点（Savepoint）**：事务处理中设置的临时占位符，可以对它发布回退（而不是回退整个事务）。

#### **关键语法与操作**

- **开始事务**：使用 `START TRANSACTION`来标识一个事务的开始。 

    ```mysql
    START TRANSACTION;
    ```

- **回退事务**：使用 `ROLLBACK`语句来撤销所有的 SQL 语句。 

    ```mysql
    START TRANSACTION;
    DELETE FROM orderitems; -- 假设误操作
    ROLLBACK; -- 撤销 DELETE 操作
    ```

- **提交事务**：使用 `COMMIT`语句在事务处理块中明确提交更改。只有在所有操作都成功后才应提交。 

    ```mysql
    START TRANSACTION;
    DELETE FROM orderitems WHERE order_num = 20010;
    DELETE FROM orders WHERE order_num = 20010;
    COMMIT; -- 只有两条 DELETE 都成功才最终生效
    ```

- **使用保留点（Savepoints）**：为了支持部分回退，可以在事务中创建占位符（保留点）。 **设置保留点**：`SAVEPOINT savepoint_name;` **回退到保留点**：`ROLLBACK TO savepoint_name;` 

    ```mysql
    START TRANSACTION;
    DELETE FROM orderitems WHERE order_num = 20010;
    SAVEPOINT sp1; -- 设置一个保留点
    DELETE FROM orders WHERE order_num = 20010;
    -- 假设此时发现错误，只想回退第二条删除
    ROLLBACK TO sp1; -- 回退到 sp1，即只撤销了第二条 DELETE
    COMMIT; -- 提交事务，最终只删除了 orderitems 中的行
    ```

- **更改默认的提交行为**：MySQL 默认是自动提交（autocommit）所有更改。为了使用事务，需要关闭此模式。 

    ```mysql
    SET autocommit = 0; -- 关闭自动提交，直到被重新开启
    ```

#### **易错点与指导原则**

| 操作/概念                       | 说明/指导原则                                                |
| ------------------------------- | ------------------------------------------------------------ |
| **哪些语句可以回滚？**          | 事务处理用来管理 `INSERT`, `UPDATE`, `DELETE`语句。你不能回滚 `SELECT`语句（没意义），也不能回滚 `CREATE`或 `DROP`操作（即使在一个事务处理块中，这些操作也会立即生效）。 |
| **隐含提交**                    | 当 `COMMIT`或 `ROLLBACK`语句执行后，事务会自动关闭。此外，一些 SQL 语句（如很多 DDL 语句）在执行时会隐含地提交当前活动的事务。 |
| **`COMMIT`和 `ROLLBACK`的位置** | 在事务中，这些是最后的语句。执行后，事务结束。               |
| **保留点的释放**                | 保留点在事务结束时（执行 `COMMIT`或 `ROLLBACK`后）会自动释放。也可以用 `RELEASE SAVEPOINT`明确释放，但 MySQL 中不常用。 |
| **`autocommit`的作用域**        | `autocommit`标志是针对每个连接（session）的，而不是服务器全局设置。 |
| **使用指导原则**                | 1. **确保数据完整性**：关键操作应放在事务中。 2. **保持事务简短**：事务在执行过程中会锁定资源，长时间的事务会降低系统并发性能。 3. **明智地使用回退**：回退本身也需要消耗资源，不应频繁使用。保留点越多，控制越灵活。 |

#### 注意事项

1. **Save Point**

   ​	可以将Savepoint非常贴切地理解为**存档点（Save Point）**。

   ​	Savepoint是事务（Transaction）内部的**命名标记点**。你可以在一个长事务执行过程中的特定步骤设置多个Savepoint，就像在玩游戏时在不同关卡手动存档。

   ​	**为何与回退整个事务不同？**关键在于**回退的粒度（Granularity）**。

   - **`ROLLBACK`（无Savepoint）**：相当于**退出游戏不存档**，所有操作被撤销，事务直接结束，回到最初状态。
   - **`ROLLBACK TO [savepoint_name]`**：相当于**读档到某个特定存档点**。它会撤销从该存档点之后到当前的所有操作，但**事务并未结束**。你仍然在事务中，可以基于这个存档点继续执行其他操作或设置新的Savepoint。

   

   2. **DROP vs DELETE：在ROLLBACK的作用**

      **功能区别**

      - **`DELETE`**：用于**删除表中的数据行（Records）**，是数据操作语言（DML）。它作用于表内的数据，不影响表本身的结构（如列、索引、权限）。
      - **`DROP`**：用于**删除数据库对象（Database Objects）**，是数据定义语言（DDL）。例如删除整个表（`DROP TABLE`）、数据库（`DROP DATABASE`）或视图。

      **为何回滚行为不同？**

      关键原因在于**事务日志（Transaction Log）** 的记录方式。

      1. **`DELETE`可回滚**：当执行`DELETE`时，数据库会详细记录被删除的**每一行数据**到事务日志中。执行`ROLLBACK`时，系统只需根据日志将数据“重放”回去即可。它是一个“可记录”的操作。
      2. **`DROP`（通常）不可回滚**：当执行`DROP TABLE`时，其操作是**立即生效且大部分数据库默认将其设置为隐式提交（Implicit Commit）**。它会直接删除表的元数据（表结构）和数据文件，并且很多数据库（如MySQL的InnoDB）不会在事务日志中记录足够的信息来重建整个表结构。因此，`ROLLBACK`无法恢复。

   

   3. **TRANSACTION 的提交失败**

      **1. 语句执行错误（Statement Execution Errors）**

      这类错误发生在事务内部执行`INSERT`、`UPDATE`、`DELETE`等语句时。

      - **常见原因**： **约束违反（Constraint Violations）**：如唯一键冲突（`Duplicate key`）、外键约束失败（`Foreign Key Constraint Fails`）。 **数据类型不匹配或溢出（Data Type Mismatch or Overflow）**：例如将字符串存入整型字段。 **锁等待超时（Lock Wait Timeout）**：在等待其他事务释放锁时超过`innodb_lock_wait_timeout`设置。
      - **对提交的影响**：**此类错误通常不会导致`COMMIT`失败，而是导致当前语句失败。** 默认情况下，事务会继续运行，你可以在事务内处理错误（例如，执行另一条语句）或选择`ROLLBACK`。只有当你发出`COMMIT`时，事务中**已成功执行**的更改才会被持久化。

      **2. 系统级错误（System-level Errors）**

      这类错误发生在更底层，通常意味着数据库服务本身出现了严重问题。

      - **常见原因**： **数据库服务器崩溃（MySQL Server Crash）** 或进程被杀死。 **客户端连接异常断开（Client Connection Abnormally Disconnects）**，如网络中断。 **磁盘空间已满（Disk Full）**，导致无法写入重做日志（Redo Log）。
      - **对提交的影响**：**这类错误是导致`COMMIT`真正失败的主要原因。** 当`COMMIT`发生时，InnoDB需要将事务日志从缓存刷新到磁盘的重做日志文件中。如果此时发生系统级故障，此过程将无法完成。

      ```mermaid
      flowchart TD
          A[Begin Transaction] --> B[Execute SQL Statements]
          B --> C{执行中是否出现<br>语句级错误?}
          C -- Yes --> D[当前语句失败]
          D --> E{是否在事务内<br>处理错误?}
          E -- No --> F[执行ROLLBACK]
          E -- Yes --> B
          C -- No --> G[语句执行成功]
          G --> H[执行COMMIT]
          H --> I{COMMIT时是否发生<br>系统级错误?}
          I -- No --> J[提交成功<br>更改持久化]
          I -- Yes --> K[提交失败<br>事务自动回滚]
      ```

      

      4. **Save Point之后的执行流程**

         **核心机制：状态回滚，程序继续**

         ​	`ROLLBACK TO SAVEPOINT`的本质是：**将数据库的数据状态回滚到设置保存点的那一刻，然后继续执行`ROLLBACK ...`语句之后的下一条指令。**

         ​	它不会自动重新执行从保存点到回滚语句之间的代码。执行权立刻交给下一行代码。**是否会形成死循环？不会**，因为数据库**不会自动重复执行**任何语句。

         ```mermaid
         flowchart TD
             A[开始事务] --> B[设置保存点: SV1]
             B --> C[执行操作A]
             C --> D{操作A是否失败？}
             D -- 是 --> E[ROLLBACK TO SV1<br>回滚到保存点状态]
             E -- 程序控制流在此继续 --> F[执行备用操作B]
             F --> G[提交事务]
             D -- 否 --> H[继续执行后续操作]
             H --> G
             E -- 程序员在此可选择 --> I[重试操作A]
             I --> D
         ```

         ```MySQL
         -- 假设有一个简单的账户表
         CREATE TABLE accounts (
             id INT PRIMARY KEY,
             balance DECIMAL(10, 2)
         );
         
         -- 初始化测试数据
         INSERT INTO accounts VALUES (1, 1000.00);
         
         -- 开始事务
         START TRANSACTION;
         
         -- 第一步操作：为账户1增加100元（这个操作会被保留）
         UPDATE accounts SET balance = balance + 100 WHERE id = 1;
         
         -- 设置一个保存点，标记当前状态
         SAVEPOINT after_first_update;
         
         -- 第二步操作：尝试为账户1扣除200元（假设此操作后可能发现逻辑问题需回滚）
         UPDATE accounts SET balance = balance - 200 WHERE id = 1;
         
         -- 假设此处根据业务逻辑判断，需要撤销扣除200元的操作，但保留增加100元的操作
         -- 回滚到保存点after_first_update：撤销其后的所有操作（即扣除200元）
         ROLLBACK TO SAVEPOINT after_first_update;
         
         -- 执行权立即交由此处下一行代码，继续执行新操作
         -- 第三步操作：为账户1扣除50元（此操作将保留）
         UPDATE accounts SET balance = balance - 50 WHERE id = 1;
         
         -- 提交事务，使所有未被回滚的操作生效
         COMMIT;
         
         -- 最终结果：账户1余额为1000 + 100 - 50 = 1050元
         ```



5. **Save Point的手动释放**

   手动释放保存点（Manually releasing a SAVEPOINT）的核心作用是**主动的资源管理（Resource Management）**。

   虽然事务结束（COMMIT/ROLLBACK）时会自动释放所有保存点，但在一个非常长或复杂的事务中，提前释放不再需要的保存点是有益的：

   1. **释放资源（Free Resources）**：每个保存点都需要数据库服务器使用少量内存来维护其状态信息。显式释放（Using `RELEASE SAVEPOINT savepoint_name;`）可以立即释放这些资源。
   2. **避免命名冲突**：在复杂的事务逻辑中，你可能会希望重用同一个保存点名称。先释放之前的，再创建一个同名的新保存点，可以使代码更清晰。
   3. **代码清晰性**：明确地释放保存点，就像在编程中释放一个临时变量一样，表明了该保存点的生命周期已经结束，提高了代码的可读性。



6. **autocommit**

   **当 `autocommit=1`(默认)**

   ​	显式书写的 `COMMIT`语句**完全有效**。当你使用 `START TRANSACTION`或 `BEGIN`开启一个显式事务（explicit transaction）时，它会临时挂起自动提交模式。此时，你必须使用 `COMMIT`来提交这个事务块内的更改。

   **当 `autocommit=0`**

   ​	所有数据修改语句（DML statements，如 `INSERT`, `UPDATE`, `DELETE`）**不会自动提交**。它们会形成一个打开的事务，更改仅对当前会话可见，直到你执行 `COMMIT`（使其永久化）或 `ROLLBACK`（撤销）。这**正是**设置 `autocommit=0`的目的。

   **关于“危险”**

   ​	`autocommit=0`确实增加了风险。如果连接结束后忘记手动提交，所有更改将丢失。更危险的是，如果下一个用户在同一连接上执行操作并提交，可能会意外地提交你之前未提交的更改。因此，**通常建议保持 `autocommit=1`（默认）**，仅在需要多语句原子性时使用显式事务。



7. **autocommit的生效范围**

   ​	**`autocommit`的设置是每个客户端连接（Client Connection）会话级别的独立配置，而不是一个全局的服务器（Server-wide）配置。**

   - **服务器** 可以有一个默认的 `autocommit`初始值（通常为1）。
   - 但当**每个客户机应用程序**（如你的命令行工具、网站程序）连接到服务器时，它们都建立了一个独立的会话（Session）。每个会话都可以根据自己的需要，使用 `SET autocommit=0`或 `SET autocommit=1`来修改自己的 `autocommit`状态，而不会影响其他连接的设置。



------





















































---

### What's More

### **外键（Foreign Key）**

外键是数据库参照完整性的核心机制，用于建立表与表之间的关联。它确保一个表（从表/子表）中的某列值必须存在于另一个表（主表/父表）的指定列（通常是主键）中。

#### **核心概念与术语**

- **主表 / 父表（Parent Table / Referenced Table）**：被引用的表，其被参考的列必须具有唯一性约束（如`PRIMARY KEY`或`UNIQUE KEY`）。
- **从表 / 子表（Child Table / Referencing Table）**：包含外键列的表，该列的值参考自主表。
- **外键约束名（Constraint Name）**：外键约束规则的唯一标识符，用于后续管理（如删除约束），与**外键列名（Column Name）**（存储实际数据的字段）是不同概念。

#### **创建外键约束**

外键约束可在建表时定义或之后添加。

- **创建表时定义（At Table Creation）** 在 `CREATE TABLE`语句的列定义后使用 `FOREIGN KEY`关键字。 `CREATE TABLE child_table (    id INT PRIMARY KEY,    parent_id INT, -- 外键列    -- 方法1：直接定义，系统自动生成约束名    FOREIGN KEY (parent_id) REFERENCES parent_table(id) ); CREATE TABLE child_table (    id INT PRIMARY KEY,    parent_id INT,    -- 方法2：使用CONSTRAINT命名约束（推荐）    CONSTRAINT fk_child_parent FOREIGN KEY (parent_id)     REFERENCES parent_table(id) );`
- **为已存在表添加（Adding to Existing Table）** 使用 `ALTER TABLE`语句添加外键约束。 `ALTER TABLE child_table ADD CONSTRAINT fk_child_parent -- 指定约束名称 FOREIGN KEY (parent_id) -- 指定本表中的外键列 REFERENCES parent_table(id); -- 引用主表及其主键列`**前提条件**：从表中外键列的现有数据必须与主表被引用列的值一致或为`NULL`，否则添加会失败。

#### **删除外键约束**

使用 `ALTER TABLE`语句，通过**约束名**而非列名来删除。

```
ALTER TABLE child_table
DROP FOREIGN KEY fk_child_parent; -- 删除名为 fk_child_parent 的外键约束
```

**关键点**：删除外键约束后，MySQL会自动创建的索引可能依然存在，如需删除该索引，需额外执行 `DROP INDEX`语句。

#### **外键约束的参照操作（Referential Actions）**

定义外键时，可通过 `ON DELETE`和 `ON UPDATE`子句设定当主表数据被删除或更新时，从表数据的处理规则。

| 操作关键字              | 描述（Description） | 对子表的影响                                                 |
| ----------------------- | ------------------- | ------------------------------------------------------------ |
| `RESTRICT`/ `NO ACTION` | 限制（默认）        | 如果子表有匹配记录，则禁止删除/更新父表对应记录。            |
| `CASCADE`               | 级联                | 父表记录被删除/更新时，同步删除/更新子表中所有关联记录。     |
| `SET NULL`              | 置空                | 父表记录被删除/更新时，将子表中对应记录的外键列值设为`NULL`（要求该列允许`NULL`）。 |
| `SET DEFAULT`           | 设为默认值          | 父表记录被删除/更新时，将子表中对应记录的外键列值设为默认值（InnoDB引擎不支持）。 |

**语法示例**：

```
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
    ON DELETE CASCADE   -- 父表记录删除时级联删除
    ON UPDATE SET NULL -- 父表主键更新时子表外键置空
);
```

#### **外键约束的关键限制与易错点**

- **存储引擎（Storage Engine）**：**必须使用InnoDB引擎**。MyISAM等引擎不支持外键约束，即使语法成功也无效。
- **数据类型匹配（Data Type Compatibility）**：外键列与主表被引用列的数据类型必须**完全一致**（如`INT`与`TINYINT`可能不兼容），否则创建失败（ERROR 1005）。
- **引用列必须唯一**：外键必须引用主表的`PRIMARY KEY`或`UNIQUE KEY`。
- **索引（Index）**：MySQL通常会自动在外键列上创建索引以提高查询性能。如果该列已有索引（需满足要求），则可能不再自动创建。
- **操作顺序**：创建时，应先创建主表，再创建从表。删除时，应先删除从表（或先删除外键约束），再删除主表。

#### **实用命令与技巧**

- **查看外键约束名**：如果忘记约束名，使用以下命令查看。 `SHOW CREATE TABLE child_table\G`
- **临时禁用外键检查**：在大量数据导入等场景，可暂时关闭外键检查提升效率。操作完成后务必重新开启。 `SET FOREIGN_KEY_CHECKS = 0; -- 禁用检查 -- 执行数据导入等操作... SET FOREIGN_KEY_CHECKS = 1; -- 重新启用检查`

---

### **MySQL流程控制语句（Flow Control Statements）**

​	MySQL流程控制语句主要用于**存储程序（Stored Programs）**中，包括存储过程（Stored Procedures）、存储函数（Stored Functions）和触发器（Triggers），用于实现条件判断、循环等复杂的业务逻辑。这些语句增强了SQL的过程化处理能力。

#### **分支结构（Branching Structure）**

用于根据条件执行不同的代码路径。

- **IF语句（IF Statement）** 语法格式如下，用于实现多条件分支判断： `IF condition1 THEN    statement_list1; ELSEIF condition2 THEN    statement_list2; ... ELSE    statement_listN; END IF;`**关键点**： 必须以 `END IF;`结束。 `ELSEIF`是连写为一个单词。 区别于 `IF()`函数（`IF(expr, true_value, false_value)`），`IF`语句用于存储程序内执行语句块，而 `IF()`函数用于表达式求值返回结果。
- **CASE语句（CASE Statement）** 提供两种形式的条件判断： **简单CASE语句（Simple CASE Statement）**：基于一个表达式的精确匹配。 `CASE case_value    WHEN when_value1 THEN statement_list1;    WHEN when_value2 THEN statement_list2;    ...    ELSE statement_listN; END CASE;`**搜索CASE语句（Searched CASE Statement）**：基于多个布尔条件判断。 `CASE    WHEN condition1 THEN statement_list1;    WHEN condition2 THEN statement_list2;    ...    ELSE statement_listN; END CASE;`**关键点**： 必须以 `END CASE;`结束。 存储程序中的 `CASE`语句与SQL查询中的 `CASE`表达式语法略有不同（如使用 `END CASE`而非 `END`）。

#### **循环结构（Loop Structure）**

用于重复执行语句块。

- **LOOP循环（LOOP Loop）** 创建一个无限循环（Infinite Loop），必须使用 `LEAVE`语句退出。 `[loop_label:] LOOP    statement_list;    IF exit_condition THEN        LEAVE loop_label; -- 退出循环    END IF; END LOOP [loop_label];`
- **WHILE循环（WHILE Loop）** 先判断条件，条件为真（TRUE）时执行循环体。属于**前测试循环（Pre-test Loop）**。 `[while_label:] WHILE condition DO    statement_list; END WHILE [while_label];`
- **REPEAT循环（REPEAT Loop）** 先执行一次循环体，再判断条件。条件为真时退出。属于**后测试循环（Post-test Loop）**。 `[repeat_label:] REPEAT    statement_list;    UNTIL condition -- 注意：此处不要加分号 END REPEAT [repeat_label];`

| 循环类型     | 功能描述         | 循环特点               | 退出条件方式                 |
| ------------ | ---------------- | ---------------------- | ---------------------------- |
| **`LOOP`**   | 基础循环         | 无限循环（死循环）     | 必须使用 `LEAVE`语句显式退出 |
| **`WHILE`**  | 带条件判断的循环 | 先判断后执行（前测试） | 条件为 **FALSE** 时退出循环  |
| **`REPEAT`** | 带条件判断的循环 | 先执行后判断（后测试） | 条件为 **TRUE** 时退出循环   |

#### **循环控制语句（Loop Control Statements）**

用于在循环内部进行精细控制。

- **LEAVE语句（LEAVE Statement）** 用于**立即退出**（跳出）指定的循环或带标签的 `BEGIN...END`块。作用类似于Java/C中的 `break`关键字。 `LEAVE label_name;`
- **ITERATE语句（ITERATE Statement）** 用于**立即结束本次循环**，跳过剩余语句，直接开始下一次循环迭代。作用类似于Java/C中的 `continue`关键字。 `ITERATE label_name;`

**核心区别**：`LEAVE`是跳出整个循环，`ITERATE`是跳过本次循环。两者都必须与**标签（Label）** 配合使用。











