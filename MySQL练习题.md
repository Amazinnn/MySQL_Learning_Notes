#### 第四章 检索数据练习题 ####

**情景：从products表中==选择==产品名称和价格。**

```MySQL
SELECT prod_name,prod_price FROM products;
```
**情景：检索products表中==不重复==的供应商ID。**
```MySQL
SELECT DISTINCT vendors_id FROM products;
```
**情景：只==显示==products表的前5行记录。**
```MySQL
SELECT * FROM products LIMIT 5;
```
**情景：==检索==products表的所有列信息。**
```MySQL
SELECT * FROM products;
```
**情景：使用==完全限定名==从crashcourse.products表选择产品名称。**
```MySQL
SELECT crashcourse.products.id FROM crashcourse.products;
```
**情景：找出==不重复==的供应商ID，并==限制==返回3个。**

```MySQL
SELECT DISTINCT id FROM products LIMIT 3;
```

#### 第五章 排序检索数据练习题 ####

**情景：对products表中的产品名称按字母顺序==排序==显示。**

```mysql
SELECT prod_name FROM products ORDER BY prod_name;
```
**情景：按产品价格降序==排序==所有产品信息。**
```mysql
SELECT * FROM products ORDER BY prod_price DESC;
```
**情景：先按供应商ID升序，再按产品价格降序==排序==产品记录。**

```mysql
SELECT * FROM products ORDER BY vend_id,prod_price DESC;
```
> [!IMPORTANT]
>
> **情景：使用列位置（如2代表价格列）==排序==产品数据。**
>
> ```mysql
> SELECT * FROM products ORDER BY 2;
> ```

**情景：先按产品价格升序排序，后按产品名称降序==排序==。**

```mysql
SELECT * FROM products ORDER BY prod_price,prod_name DESC;
```
**情景：按计算字段（如价格乘以数量）==排序==订单项。**
```mysql
SELECT * FROM orders ORDER BY orders.price*orders.num;
```
#### 第六章 过滤数据练习题 ####

**情景：从products表中选择价格==大于==10的产品。**
```mysql
SELECT prod_name FROM products WHERE products.prod_price >10;
```
**情景：选择价格在5到20之间（==包含==边界）的产品。**
```mysql
SELECT prod_name FROM products WHERE prod_price>=5 AND prod_price <=20;
```
**情景：选择供应商ID为1003==或==1005的产品。**
```mysql
SELECT prod_name FROM products WHERE prod_vendor IN (1003,1005);
```
> [!IMPORTANT]
>
> **情景：选择产品描述不为==空值==的产品。**
>
> ```mysql
> SELECT prod_name FROM products WHERE prod_description NOT NULL;		-- 错误！！要添加'IS'！！
> SELECT prod_name FROM products WHERE prod_description IS NOT NULL;
> ```

> [!IMPORTANT]
>
> **情景：选择价格小于10==且==供应商为1002的产品。**
>
> ```mysql
> SELECT prod_name FROM products WHERE prod_price<10 AND vend_id='1002';
> -- 错误！！供应商ID是数字，不应该用引号，除非是字符串类型！！
> SELECT prod_name FROM products WHERE prod_price<10 AND vend_id=1002;
> ```

**情景：选择订单日期在2023年1月1日==之后==的订单。**

```mysql
SELECT orders.item FROM orders WHERE orders.date >'2023-1-1';
```
#### 第七章 数据过滤练习题 ####

**情景：选择供应商ID==不在==列表(1001,1002)中的产品。**
```mysql
SELECT prod_name FROM products 
WHERE prod_vendor NOT IN (1001,1002);
```
> [!IMPORTANT]
>
> **情景：使用==子查询==选择已下过订单的客户名称。**
>
> ```mysql
> SELECT cust_name FROM customers 
> WHERE cust_id IN (SELECT cust_id FROM orders);
> 
> SELECT c.cust_name FROM customers,orders 
> WHERE customers.cust_id=orders.cust_id;
> -- 可能出现重复的客户信息！！
> SELECT DISTINCT c.cust_name FROM customers,orders 
> WHERE customers.cust_id=orders.cust_id;
> -- 添加DISTINCT关键字
> SELECT c.cust_name
> FROM customers AS c,orders AS o
> WHERE c.cust_id=o.cust_id
> GROUP BY c.cust_name;
> -- 使用分组，且还支持获取其它聚合信息
> ```

**情景：选择价格==不介于==5和10之间的产品（用NOT操作）。**
```mysql
SELECT prod_name FROM products 
WHERE prod_price NOT BETWEEN 5 AND 10;
```
**情景：使用==IN==操作符选择指定供应商的产品。**

```mysql
SELECT prod_name FROM products 
WHERE vend_id IN (SELECT vendor.vend_id FROM vendors);
```
> [!IMPORTANT]
>
> **情景：选择==没有==下过订单的客户（用NOT EXISTS）。**
>
> ```mysql
> SELECT cust_name FROM customers
> WHERE NOT EXISTS(
>     SELECT 1 FROM orders
> 	WHERE orders.cust_id=customers.cust_id);
> ```

**情景：选择产品价格高于==平均值==的产品（用子查询）。**

```mysql
SELECT prod_name FROM products 
WHERE prod_price IN (
    SELECT prod_price FROM products 
    WHERE prod_price >AVG(products.prod_price));
-- 错误！！WHERE语句不能含有聚合函数！！
SELECT prod_name FROM products 
WHERE prod_price>(SELECT AVG(prod_price) FROM products);
```
#### 第八章 用通配符进行过滤练习题 ####

**情景：选择产品名称以“jet”==开头==的产品。**
```mysql
SELECT prod_name FROM products WHERE prod_name LIKE 'jet%';
```
**情景：选择产品名称包含“==anvil==”的产品。**

```mysql
SELECT prod_name FROM products WHERE prod_name LIKE '%anvil%';
```
**情景：选择产品名称以“==s==”结尾的产品。**

```mysql
SELECT prod_name FROM products WHERE prod_name LIKE '%s';
```
**情景：选择产品名称第二个字符为“==o==”的产品。**

```mysql
SELECT prod_name FROM products WHERE prod_name LIKE '_o%';
```
> [!IMPORTANT]
>
> **情景：选择产品描述以“heavy”开头且包含“duty”的产品（用==组合通配符==）。**
>
> ```mysql
> SELECT prod_name FROM products WHERE prod_name LIKE 'heavy%duty%';	
> ```

**情景：选择产品名称长度为5个字符的产品（用==下划线==匹配）。**

```mysql
SELECT prod_name FROM products WHERE prod_name LIKE '_____';
```

#### 第九章 用正则表达式进行搜索练习题 ####

**情景：使用正则表达式查找产品名称以数字==开头==的记录。**

```MySQL
SELECT * FROM products WHERE prod_name REGEXP '^[[:digit:]]';
SELECT * FROM products WHERE prod_name REGEXP '^[0-9]';
```

**情景：用正则表达式匹配包含"heavy"==或=="light"的产品描述。**
```MySQL
SELECT description FROM products WHERE description REGEXP BINARY 'heavy|light';
```
**情景：查找产品代码格式为"==ANV-##=="（ANV-后跟两位数字）的产品。**
```MySQL
SELECT prod_name FROM products WHERE prod_id REGEXP 'ANV-[[:digit:]]{2}';
```
**情景：用正则表达式选择描述中包含"==version== 1.0"到"version 9.9"的产品。**
```MySQL
SELECT prod_name FROM products WHERE prod_description REGEXP 'version [1-9]\\.[0-9]';
```
**情景：使用字符类匹配产品名称中第三个字符是==元音字母==的记录。**
```MySQL
SELECT * FROM products WHERE prod_name REGEXP '^..[aeiou]';
```
**情景：用正则表达式查找名称以"==jetpack=="开头，后跟一个空格和任意两个字符的产品。**

```MySQL
SELECT prod_name FROM products WHERE prod_name REGEXP '^jetpack ..$';
```
> [!IMPORTANT]
>
> **情景：使用定位符匹配以"==important=="开头或以"==urgent=="结尾的产品的注释文本。**
>
> ```MySQL
> SELECT prod_text FROM products WHERE prod_text REGEXP '^important|urgent$';
> -- 正确，但是推荐使用括号( )提高优先级、使表达清晰。
> -- 正则表达式中|的优先级很低
> SELECT prod_text FROM products WHERE prod_text REGEXP '^(important)|(urgent)$';
> ```

#### 第十章 创建计算字段练习题 ####

**情景：将供应商名称和国家==拼接==成"供应商名(国家)"格式显示。**
```MySQL
SELECT CONCAT(vendor.name(vendor.country)) FROM vendors;
-- 错误！CONCAT函数的参数应该是用逗号分隔，且括号要用单引号括起来！
SELECT CONCAT(vendor.name,'(',vendor.country,')') 
FROM vendors;
```
**情景：计算每个订单项的==总价==（数量×单价）。**

```MySQL
SELECT orders.item,orders.quantity*orders.unit_price AS totalprice FROM orders;
```
**情景：将客户姓名和城市用=="-"分隔符连接==显示。**

```MySQL
SELECT CONCAT(cust.name,'-',cust.city) 
FROM customers AS cust;
```
**情景：计算产品价格打9折后的==折扣价==。**

```MySQL
SELECT prod,0.9*price FROM products;
```
**情景：将供应商地址的各部分用逗号==连接==成完整地址。**
```MySQL
SELECT CONCAT(state,',',county,',',town) 
AS address FROM vendors;
```
> [!IMPORTANT]
>
> **情景：计算库存价值（价格×库存数量）并按==千分位==格式化。**
>
> ```MySQL
> SELECT FORMAT(prod_price*prod_quantity, 2) 
> FROM products;
> -- FORMAT函数会返回一个字符串，并且第二个参数是小数位数。
> ```

#### 第十一章 使用数据处理函数练习题 ####

**情景：将客户姓名转换为==大写字母==显示。**
```MySQL
SELECT Upper(cust_name) FROM customers;
```
> [!IMPORTANT]
>
> **情景：提取订单日期中的==年份==进行统计。**
>
> ```MySQL
> SELECT orders.id,Year(date) FROM orders; 
> -- order是关键字，可以添加反引号``或者改用orders
> SELECT `order`.id,Year(date) FROM `order`; 
> ```

**情景：计算客户姓名的==字符长度==并按长度排序。**
```MySQL
SELECT cust_name, LENGTH(cust_name) AS name_length 
FROM customers ORDER BY name_length;
```
**情景：将产品价格==四舍五入==到整数显示。**
```MySQL
SELECT ROUND(prod_price) FROM products;
```
**情景：获取==当前日期==并显示3天前的日期。**
```MySQL
SELECT CURDATE(), DATE_SUB(CURDATE(), INTERVAL 3 DAY);
```
**情景：使用Soundex函数查找与"==Jon==发音相似"的客户名。**

```MySQL
SELECT cust.name FROM customers AS cust WHERE SOUNDEX(cust.name)=SOUNDEX('Jon');
```
**情景：计算每个订单的==天数差==（当前日期-订单日期）。**
```MySQL
SELECT DATEDIFF(CURDATE(), order_date) 
AS days_diff FROM orders;
```
#### 第十二章 汇总数据练习题 ####

**情景：计算所有产品的==平均价格==。**
```MySQL
SELECT AVG(prod_price) FROM products;
```
**情景：统计每个供应商的==产品数量==。**
```MySQL
SELECT p.vend_id,COUNT(p.quantity) 
FROM products AS p GROUP BY vend_id;
```
**情景：找出价格==最高==的产品价格。**
```MySQL
SELECT MAX(price) FROM products;
```
**情景：计算某个客户所有订单的==总金额==。**
```MySQL
SELECT cust,SUM(total_amount) FROM orders GROUP BY cust;
```
**情景：统计不同供应商的==唯一产品数量==（去重计数）。**
```MySQL
SELECT vend_id,COUNT(DISTINCT vend_id) 
FROM products GROUP BY vend_id;
```
**情景：计算每个产品类别的==平均价格和总库存==。**
```MySQL
SELECT category,AVG(prod_price),SUM(prod_quantity) 
FROM products GROUP BY category;
```
**情景：统计每个客户的==订单数量==和==平均订单金额==。**
```MySQL
SELECT cust,COUNT(*),AVG(amount) FROM orders GROUP BY cust;
```

#### 第十三章 分组数据练习题 ####

**情景：统计每个供应商提供的==不同价格==的产品数量。**

```MySQL
SELECT vendor,COUNT(DISTINCT price) AS prod_of_diff_price
FROM products GROUP BY vendor;
```

**情景：按供应商分组，计算每个供应商产品的==平均价格==。**

```MySQL
SELECT vendor,AVG(price) FROM products GROUP BY vendor;
```
**情景：找出订单数量==超过5个==的客户ID及订单数量。**

```MySQL
SELECT cust,COUNT(cust_order) FROM orders GROUP BY cust HAVING COUNT(cust_order)>5;
```
**情景：按产品类别分组，显示每个类别的==最高价和最低价==。**

```MySQL
SELECT prod_type,MAX(price) AS MaxPrice,MIN(price) AS MinPrice
FROM products GROUP BY prod_type;
```
> [!IMPORTANT]
>
> **情景：统计每个客户在2023年的==订单总金额==。**
>
> ```MySQL
> SELECT cust,SUM(amount) FROM orders GROUP BY cust WHERE Year(date)='2023';
> -- 错误！WHERE子句应该在GROUP BY之前，而且不能有GROUP BY的条件！
> SELECT cust_id, SUM(order_amount) FROM orders WHERE YEAR(order_date)=2023 GROUP BY cust_id;
> ```

**情景：按供应商和国家分组，计算每个组的==产品数量==。**
```MySQL
SELECT vendor,nation,SUM(amount) AS prod_amount
FROM products GROUP BY vendor,nation;
```
**情景：找出平均订单金额==大于1000==的客户及平均金额。**
```MySQL
SELECT cust,AVG(amount) AS avg_amount FROM orders 
GROUP BY cust HAVING AVG(amount)>1000;
```
#### 第十四章 使用子查询练习题 ####

**情景：使用子查询找出价格==高于平均价==的所有产品。**
```MySQL
SELECT prod_name FROM products WHERE price >
(SELECT AVG(price) FROM products);
```
**情景：找出订购了产品'TNT2'的==客户姓名==（用子查询实现）。**
```MySQL
SELECT cust_name FROM customers WHERE cust_id IN
(SELECT cust_id FROM orders WHERE order_detail REGEXP 'TNT2');
```
**情景：使用IN子查询找出从未下过订单的==客户信息==。**
```MySQL
SELECT cust_name,cust_info FROM customers WHERE cust_id NOT IN
(SELECT cust_id FROM orders);
```
> [!IMPORTANT]
>
> **情景：在SELECT子句中使用子查询显示每个客户的==订单数量==。**
>
> ```MySQL
> SELECT cust_name,(SELECT COUNT(*) FROM orders
> WHERE orders.cust_id=customers.cust_id) FROM customers;
> ```

**情景：使用EXISTS子查询检查是否有客户==购买过==特定产品。**

```MySQL
SELECT cust_name FROM customers WHERE EXISTS 
(SELECT 1 FROM orders WHERE orders.cust_id=customers.cust_id);
```
**情景：找出订单金额==超过==该客户平均订单金额的订单。**

```MySQL
SELECT c.cust_name,o.cust_order,o.amount 
FROM customers AS c,orders AS o
WHERE (c.cust_id=o.cust_id AND 
       o.amount>(SELECT AVG(amount) 
       FROM o WHERE c.cust_id=o.cust_id));

SELECT c.cust_name,o.cust_order,o.amount
FROM customers AS c INNER JOIN orders AS o
ON c.cust_id=o.cust_id
WHERE o.amount>
(SELECT AVG(o.amount) FROM o WHERE c.cust_id=o.cust_id);
```
**情景：使用相关子查询显示每个产品的==价格排序==（在该供应商内）。**

```MySQL
SELECT v.vend_name,p.prod_amount FROM vendors AS v INNER JOIN products AS p
ON (v.vend_id=p.vend_id) ORDER BY p.prod_amount;
```
#### 第十五章 联结表练习题 ####

**情景：通过等值联结显示每个产品的==供应商名称==和产品信息。**

```MySQL
SELECT v.vend_name,p.info FROM vendor AS v,products AS p
WHERE v.vend_id=p.vend_id;
SELECT v.vend_name,p.info FROM vendor AS v INNER JOIN products AS p
ON v.vend_id=p.vend_id;
```
**情景：使用内部联结查询每个订单的==客户姓名==和订单日期。**

```MySQL
SELECT o.item,c.cust_name,o.date FROM customers AS c INNER JOIN orders AS o
ON c.cust_id=o.cust_id;
```
**情景：通过多表联结显示订单详情，包括==产品名称和供应商==。**

```MySQL
SELECT o.item,o.info,c.cust_name,v.vend_name
FROM orders AS o INNER JOIN customers AS c INNER JOIN vendors AS v
ON (c.cust_id=o.cust_id AND v.vend_id=o.vend_id);
```
> [!IMPORTANT]
>
> **情景：使用联结找出价格==最高==的产品及其供应商信息。**
>
> ```MySQL
> SELECT p.prod_name,p.prod_price,v.vend_name,v.vend_info
> FROM products AS p INNER JOIN vendors AS a
> ON (p.vend_id=v.vend_id AND 
>    p.prod_price=MAX(p.prod_price));
>    -- 错误！WHERE条件中不能使用聚合函数，而且ON条件中也不能！
> SELECT p.prod_name, p.prod_price, v.vend_name, v.vend_info
> FROM products AS p INNER JOIN vendors AS v 
> ON p.vend_id = v.vend_id
> WHERE p.prod_price = 
> (SELECT MAX(p.prod_price) FROM products);
> -- 应该先子查询找出最高价格！
> ```

#### 第十六章 创建高级联结练习题 ####

**情景：通过自联结找出同一供应商的==其他产品==信息。**
```MySQL
SELECT p1.prod_name,p2.prod_name,p2.prod_info
FROM products AS p1 INNER JOIN products AS p2
ON (p2.vend_id=p1.vend_id);
```
**情景：使用左外部联结显示==所有客户==及其订单（包括无订单客户）。**
```MySQL
SELECT c.cust_name,o.item
FROM customers AS c LEFT OUTER JOIN orders AS o
ON (c.cust_id=o.cust_id);
```
**情景：通过右外部联结显示==所有订单==对应的客户信息。**
```MySQL
SELECT o.item,c.cust_name,c.cust_info
FROM customers AS c RIGHT OUTER JOIN orders AS o
ON (c.cust_id=o.cust_id);
```
**情景：在外部联结中使用聚集函数统计每个客户的==订单总数==。**
```MySQL
SELECT c.cust_name,COUNT(o.item) AS TotalOrders
FROM customers AS c LEFT OUTER JOIN orders AS o
ON (c.cust_id=o.cust_id);
```
**情景：使用自联结找出==员工及其经理==的对应关系（假设有员工表）。**

```MySQL
SELECT e1.name AS worker, e2.name AS manager
FROM employees AS e1 INNER JOIN employees AS e2 
ON e1.manager_id = e2.employee_id;
```

#### 第十七章 组合查询（UNION）练习题 ####

**情景：使用UNION合并价格低于10和供应商为1001的==产品查询结果==。**

```MySQL
SELECT prod_name,prod_price,vend_id FROM products WHERE prod_price<10
UNION SELECT prod_name,prod_price,vend_id FROM products WHERE vend_id=1001;
```

> [!IMPORTANT]
>
> **情景：将客户姓名和供应商名称合并到一个==结果集中==显示。**
>
> ```MySQL
> SELECT vend_name AS name FROM vendors
> UNION SELECT cust_name FROM customers;
> -- 第一条查询的列名会成为查询结果的列名!
> ```

**情景：使用UNION ALL合并两个产品查询，==保留重复记录==。**
```MySQL
SELECT prod_name,prod_info FROM products_1
UNION ALL SELECT prod_name,prod_info FROM products_2;
```
**情景：合并三个不同价格区间的产品查询，并按价格==统一排序==。**

```MySQL
SELECT prod_name,prod_price,prod_info FROM products_1
UNION SELECT prod_name,prod_price,prod_info FROM products_2
UNION SELECT prod_name,prod_price,prod_info FROM products_3
ORDER BY prod_price;
```
**情景：使用UNION合并客户表和供应商表的联系信息，并==去除重复==。**
```MySQL
SELECT cust_name AS name,cust_tele AS contact FROM customers
UNION SELECT vend_name,vend_tele FROM vendors;
```
**情景：将活跃客户和近期订单客户合并为==一个联系人列表==。**

```MySQL
SELECT cust_name,cust_tele FROM active_customers
UNION SELECT cust_name,cust_tele FROM customers;
```
> [!IMPORTANT]
>
> **情景：使用UNION合并2024与2025年的订单数据（两表数据类型相同），	并==统计两年订单的总数==。**
>
> ```MySQL
> SELECT * FROM orders_2024
> UNION ALL
> SELECT * FROM orders_2025;
> SELECT COUNT(*) AS total_orders
> FROM (
>     SELECT * FROM orders_2024
>     UNION ALL
>     SELECT * FROM orders_2025
> ) AS combined_orders;
> ```

**情景：合含有'heavy'并产品描述搜索和含有'car'产品名称搜索的==全文本结果==。**

```MySQL
SELECT prod_name,prod_description FROM products
WHERE MATCH(prod_description) AGAINST ('heavy')
UNION ALL SELECT prod_name,prod_description FROM products
WHERE MATCH(prod_name) AGAINST ('car');
```
#### 第十八章 全文本搜索练习题 ####

> [!IMPORTANT]
>
> **情景：在产品描述中搜索包含"==heavy duty=="的==完整短语==。**
>
> ```MySQL
> SELECT prod_description FROM products
> WHERE MATCH(prod_description) AGAINST ('heavy duty');
> -- 错误！普通全文本搜索不支持短语，须布尔模式！短语要用双引号！
> 
> SELECT prod_description FROM products
> WHERE MATCH(prod_description) 
> AGAINST ('"heavy duty"' IN BOOLEAN MODE);
> ```

> [!IMPORTANT]
>
> **情景：使用全文本搜索查找描述中包含"anvil"或"hammer"的产品。**
>
> ```MySQL
> SELECT prod_name,prod_description FROM products
> WHERE MATCH(prod_description) 
> AGAINST ('anvil|hammer' IN BOOLEAN MODE);
> -- 错误！布尔模式下使用|表示OR操作符无效！
> 
> SELECT prod_name, prod_description FROM products
> WHERE MATCH(prod_description) 
> AGAINST('anvil hammer' IN BOOLEAN MODE);
> -- 方式一：使用空格（简洁）
> 
> SELECT prod_name, prod_description FROM products
> WHERE MATCH(prod_description) 
> AGAINST('anvil OR hammer' IN BOOLEAN MODE);
> -- 方式二：使用OR关键字（清晰）
> ```

**情景：通过全文本搜索按==相关性排序==描述中包含"safe"的产品。**

```MySQL
SELECT prod_description FROM products
WHERE MATCH(prod_description) AGAINST ('safe');
-- MySQL会默认按相关性从高到低排序
-- 但若想显式的排序：
SELECT prod_description,
	(MATCH(prod_description) AGAINST ('safe')) AS relevance
	FROM products
WHERE MATCH(prod_description) AGAINST ('safe')
ORDER BY relevance;
```
**情景：使用查询扩展搜索产品描述中的"==nut=="，包含相关词汇的结果。**

```MySQL
SELECT prod_description FROM products
WHERE MATCH(prod_description) AGAINST ('nut' WITH QUERY EXPANSION);
```
**情景：使用布尔模式搜索必须包含"==warning=="但不能包含"caution"的产品描述。**

```MySQL
SELECT prod_description FROM products
WHERE MATCH(prod_description) AGAINST ('+warning-caution' IN BOOLEAN MODE);
```
**情景：搜索产品名称以"==jetpack=="开头且描述包含"fuel"的产品记录。**

```MySQL
SELECT prod_name,prod_description FROM products
WHERE MATCH(prod_description) AGAINST ('+fuel' IN BOOLEAN MODE)
	AND prod_name REGEXP '^jetpack';
```
> [!IMPORTANT]
>
> **情景：使用全文本搜索查找包含"==version 1.0=="到"version 2.0"的文档。**
>
> ```MySQL
> SELECT prod_description FROM products
> WHERE MATCH(prod_description) 
> 	AGAINST('+version +1.0..2.0' IN BOOLEAN MODE);
> ```

#### 第十九章 插入数据练习题 ####

**情景：向customers表插入一条==完整的新客户==记录。**
```MySQL
INSERT INTO customers(cust_name,cust_id,cust_info)
VALUES('jack',1001,'an active fruit procurement clients');
```
**情景：只插入客户姓名和地址，让数据库==自动生成==客户ID。**
```MySQL
CREATE TABLE customers
(
	cust_id INT AUTO_INCREMENT,
    cust_name VARCHAR(20),
    cust_address VARCHAR(100),
    PRIMARY KEY(cust_id)
)Engine=InnoDB;
INSERT INTO customers(cust_name,cust_address)
VALUES ('jack','123 Main Street,Apt 4B NewYork,NY 1001');
```
**情景：一次性向products表插入==多个新产品==记录。**

```MySQL
INSERT INTO products(prod_name,prod_info)
VALUES(……),(……),(……);
```
> [!IMPORTANT]
>
> **情景：使用INSERT SELECT将旧客户数据==复制到新表==。**
>
> ```MySQL
> INSERT INTO newtable(a,b,c……)
> SELECT a,b,c…… FROM oldtable;
> 
> INSERT INTO newtable
> SELECT * FROM oldtable;
> -- 不推荐！极易因为表结构变动而导致数据错位或失效！
> ```

**情景：插入订单时省略允许NULL的列，使用==默认值==。**
```MySQL
CREATE TABLE orders
(
	ord_item VARCHAR(20) PRIMARY KEY,
    ord_info VARCHAR(200),
    cust_id INT,
    cust_name VARCHAR(20) DEFAULT NULL
);
INSERT INTO orders(ord_item,ord_info,cust_id)
VALUES('apples','……',1001);
```
> [!IMPORTANT]
>
> **情景：向有外键约束的orderitems表插入==有效关联数据==。**
>
> ```MySQL
> INSERT INTO orderitems(order_num, prod_id, quantity, item_price)
> VALUES(20005, 'ANV01', 10, 5.99);
> -- 前提是20005在orders表中存在，'ANV01'在products表中存在
> ```

**情景：使用LOW_PRIORITY插入数据，==降低操作优先级==。**
```MySQL
INSERT LOW_PRIORITY INTO `table_name`(……) VALUES(……);
```
**情景：插入包含特殊字符和==引号==的产品描述文本。**
```MySQL
INSERT INTO products(prod_name, prod_description)
VALUES('Special Product',
       'This product''s description contains "quotes"');
```
#### 第二十章 更新和删除数据练习题 ####

**情景：更新客户编号从1001到1010的==电子邮件地址==信息为'unknown'。**

```MySQL
UPDATE customers SET email = 'unknown'
WHERE customers.cust_id BETWEEN 1001 AND 1010;
```
**情景：将ID为1001的供应商的所有产品价格==上调10%==。**

```MySQL
UPDATE vendors SET unit_price=1.1*unit_price
WHERE vend_id = 1001;
```
**情景：删除ID为1010的客户的==所有订单记录==。**

```MySQL
DELETE FROM orders WHERE cust_id = 1010;
```
**情景：使用子查询为购买了特定产品的客户提升==会员等级==。**

```MySQL
UPDATE customers SET cust_level = cust_level+1
WHERE cust_id IN(
	SELECT DISTINCT cust_id FROM orders
	);
```
**情景：清空临时表中的==所有数据==但不删除表结构。**

```MySQL
DELETE FROM 'table_name';
```
**情景：将有多个订单的客户标记为VIP并==更新状态==。**
```MySQL
UPDATE customers SET cust_state = 'VIP'
WHERE cust_id IN
	(SELECT cust_id FROM orders 
     GROUP BY cust_id HAVING COUNT(*) >= 2);
```
**情景：删除价格高于100且==库存为零==的陈旧产品记录。**

```MySQL
DELETE FROM products
WHERE price >100 AND quantity = 0;
```
> [!IMPORTANT]
>
> **情景：更新订单日期为NULL的记录的==默认日期值==。**
>
> ```MySQL
> UPDATE orders SET date
> WHERE date = NULL;
> -- 语法错误！
> 
> UPDATE orders SET date = DEFAULT
> WHERE date IS NULL;
> -- DEFAULT是一个特殊关键字，在此代表该字段在表定义时设置的默认值
> ```
>

#### 第二十一章 创建和操纵表练习题

**情景：创建新表时指定产品ID为==主键==并设置==自动增长==。**

```MySQL

```

**情景：为订单表定义==复合主键==，包含订单号和产品ID。**

```MySQL

```

**情景：创建供应商表时明确指定存储引擎为==InnoDB==。**
```MySQL

```
**情景：使用==ALTER TABLE==语句为产品表添加库存数量列。**
```MySQL

```
**情景：通过==ALTER TABLE==删除客户表中不再需要的备用电话列。**
```MySQL

```
**情景：为订单项表添加==外键约束==，引用产品表的主键。**
```MySQL

```
**情景：删除临时表时使用==IF EXISTS==避免表不存在错误。**
```MySQL

```
**情景：使用==RENAME TABLE==语句将旧订单表重命名为归档订单表。**
```MySQL

```
#### 第二十二章 使用视图练习题

**情景：创建视图简化客户订单信息的==多表联结==查询。**
```MySQL

```
**情景：通过视图只显示邮箱地址==不为空==的客户记录。**
```MySQL

```
**情景：创建视图将供应商名称和国家==格式化为==统一显示格式。**
```MySQL

```
**情景：尝试通过视图更新包含==GROUP BY==子句的汇总数据。**
```MySQL

```
**情景：使用==CREATE OR REPLACE VIEW==更新视图定义以添加新列。**
```MySQL

```
**情景：删除不再需要的客户邮箱列表视图。**
```MySQL

```
**情景：创建视图计算每个订单的==总金额==作为计算字段。**
```MySQL

```
**情景：通过视图查询数据时注意==性能影响==，避免复杂嵌套。**
```MySQL

```
#### 第二十三章 使用存储过程练习题 ####

**情景：创建一个存储过程来==更新产品价格==，接受产品ID和新价格作为参数。**

```MySQL
CREATE PROCEDURE price_update
(IN id INT,IN new_price INT)
BEGIN
	UPDATE products SET prod_price = new_price
	WHERE prod_id = id;
END;
```

**情景：编写存储过程==查询指定客户==的所有订单信息，返回结果集。**
```MySQL
CREATE PROCEDURE order_select(IN id INT)
BEGIN
	SELECT c.cust_name,c.cust_id,o.prod_name,o.prod_info
	FROM customers AS c INNER JOIN orders AS o 
	ON c.cust_id = id AND o.cust_id = id;
END;
```
**情景：创建存储过程，输入==订单编号==，通过OUT参数返回该订单的==总金额==**

**（总金额 = 订单明细中所有商品的单价 × 数量之和）。**

```MySQL
CREATE PROCEDURE TotalAmount(IN id INT,OUT total_amount DECIMAL(10,2))
BEGIN
	SELECT SUM(SELECT o.unit_price*o.quantity FROM orders AS o)
	INTO total_amount;
END;
```
**情景：设计存储过程==处理新订单==，包括插入订单头和订单明细，或者更新订单信息的完整事务。**

```MySQL
CREATE PROCEDURE handling_new_order
(IN id INT,IN quantity INT,IN info VARCHAR(1000))
BEGIN
	INSERT INTO orders(prod_id,prod_quantity,prod_info)
	VALUES(id,quantity,info);
END;
```
**情景：创建带条件逻辑的存储过程，根据订单金额==应用不同折扣率==。**

```MySQL
CREATE PROCEDURE Discount(IN id INT,OUT total_amount DECIMAL(10,2))
BEGIN
	DECLARE t DECIMAL(10,2);
	SELECT SUM(SELECT prod_price*prod_quantity FROM orders WHERE ord_id = id)
	INTO t;
	IF t > 100 THEN SET total_amount = t*0.8;
	ELSEIF t > 60 THEN SET total_amount = t*0.9;
	ELSE SET total_amount = t;
END;
```
**情景：设计存储过程==验证用户输入==参数的有效性，防止SQL注入。**

```MySQL
CREATE PROCEDURE is_input_valid(IN x INT,OUT answer VARCHAR(20))
BEGIN
	IF X >= 0 OR X <=10 THEN SET answer = 'Valid Input';
	ELSE SET answer = 'Invalid Input';
END;
```
#### 第二十四章 使用游标练习题 ####

**情景：使用游标==逐行处理==订单表中的订单金额计算税费。**
```MySQL
CREATE PROCEDURE tax_calculating()
BEGIN
	DECLARE done BOOLEAN DEFAULT 0;
	DECLARE id INT;
	DECLARE tax DECIMAL(8,2);
	
	CREATE TABLE IF NOT EXISTS orders_tax
	(ord_id INT,ord_tax DECIMAL(8,2),PRIMARY KEY(ord_id));
	
	DECLARE order_ids CURSOR
	FOR SELECT ord_id FROM orders;
	DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;
	OPEN order_ids;
	
	REPEAT
		FETCH order_ids INTO id;
		SELECT (orders.total_amount*0.02) FROM orders WHERE orders.id = id
		INTO tax;
		INSERT INTO orders_tax VALUES(id,tax);
	UNTIL done END REPEAT;
	
	CLOSE order_ids;
END;	
```
**情景：创建存储过程使用游标==遍历产品列表==，动态生成库存预警报告。**

```MySQL
CREATE PROCEDURE warning()
BEGIN
	DECLARE done BOOLEAN DEFAULT 0;
	DECLARE ID INT;
	DECLARE quantity INT;
	CREATE TABLE IF NOT EXISTS warning_list
	(
    	prod_id INT PRIMARY KEY,
        prod_quantity INT,
        prod_state VARCHAR(20)
    );
	DECLARE productID CURSOR FOR
	SELECT prod_id FROM products;
	DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;
	OPEN productID;
	REPEAT
		FETCH productID INTO ID;
		SELECT prod_quantity FROM products WHERE prod_id = ID
		INTO quantity;
		IF quantity = 0 THEN INSERT INTO warning_list
		VALUES(prod_id,quantity,'Out Of Stock');
		ELSEIF quantity <= 10 THEN INSERT INTO warning_list
		VALUES(prod_id,quantity,'Very Low Stock');
        ELSE IF quantity <= 20 THEN INSERT INTO warning_list
		VALUES(prod_id,quantity,'Low Stock');
		END IF;
	UNTIL done END REPEAT;
	CLOSE productID;
END;
```
**情景：使用游标和条件语句==分类统计==不同价格区间的产品数量。**
```MySQL
CREATE PROCEDURE categorical_statistics()
BEGIN
	CREATE TABLE IF NOT EXISTS sum_of_products
	VALUES(price_range VARCHAR(20) PRIMARY KEY,prod_amount INT DEFAULT 0);
	INSERT INTO sum_of_products(price_range) VALUES('0~50'),('51~100'),('101~');
	
	CREATE IDptr CURSOR FOR SELECT prod_id FROM products;
	
	DECLARE done BOOLEAN DEFAULT 0;
	DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;
	DECLARE id INT;
	DECLARE unit_price INT;
	DECLARE quantity INT;
	
	OPEN IDptr;
	
	REPEAT
		FETCH IDptr INTO id;
		SELECT prod_price,prod_quantity FROM products WHERE prod_id = id
		INTO unit_price,quantity;
		IF unit_price BETWEEN 0 AND 50 THEN UPDATE UPDATE sum_of_products
			SET prod_amount = prod_amount + quantity 
			WHERE price_range = '0~50';
		ELSEIF unit_price BETWEEN 51 AND 100 THEN UPDATE UPDATE 					sum_of_products
			SET prod_amount = prod_amount + quantity 
			WHERE price_range = '51~100';
		ELSE UPDATE UPDATE sum_of_products
			SET prod_amount = prod_amount + quantity 
			WHERE price_range = '100~';
		END IF;
	UNTIL done END REPEAT;
	
	CLOSE IDptr;
END;
```

#### 第二十五章 使用触发器练习题 ####

**情景：创建BEFORE INSERT触发器==自动格式化==新插入的产品名称为大写。**

```MySQL
CREATE TRIGGER upper_rename BEFORE INSERT
ON products FOR EACH ROW
BEGIN
	SET NEW.prod_name = Upper(NEW.prod_name);
END;
```

**情景：使用AFTER UPDATE触发器==记录==产品价格变更历史到日志表。**

```MySQL
CREATE TRIGGER price_record BEFORE UPDATE
ON products FOR EACH ROW
BEGIN
	INSERT INTO prod_log(prod_name,old_price,new_price)
	VALUES(NEW.prod_name,OLD.prod_price,NEW.prod_price);
END;	
```

**情景：通过BEFORE DELETE触发器==防止误删==重要客户记录。**

```MySQL
CREATE TRIGGER avoid_deleting_vip BEFORE DELETE
ON customers FOR EACH ROW
BEGIN
	IF (OLD.cust_state = 'vip') 
	THEN SIGNAL SQLSTATE '45000'
	SET MESSAGE_TEXT 'Cannot delete the VIP customer!';
	END IF;
END;
```

**情景：创建触发器==自动计算==订单总金额并更新到订单表。**

```MySQL
CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    total_amount DECIMAL(10, 2) DEFAULT 0
);

CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    price DECIMAL(10, 2),
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
);

CREATE TRIGGER order_item_after_insert AFTER INSERT
ON order_items FOR EACH ROW
BEGIN
	UPDATE orders
	SET total_amount = total_amount + NEW.price*NEW.quantity
	WHERE order_id = NEW.order_id;
END;

CREATE TRIGGER after_order_item_update
AFTER UPDATE ON order_items
FOR EACH ROW
BEGIN
    UPDATE orders 
    SET total_amount = total_amount + (NEW.price * NEW.quantity - OLD.price * OLD.quantity)
    WHERE order_id = NEW.order_id;
END;

CREATE TRIGGER after_order_item_delete
AFTER DELETE ON order_items
FOR EACH ROW
BEGIN
    UPDATE orders 
    SET total_amount = total_amount - (OLD.price * OLD.quantity)
    WHERE order_id = OLD.order_id;
END;
```

**P S :**

​	如果你尝试向 `orderitems`表插入一条记录，但这条记录中的 `orderid`值在 `orders`表中不存在，MySQL 数据库将会阻止这次插入操作，并返回一个外键约束错误，提示信息通常是 `ERROR 1452: Cannot add or update a child row`。这是因为外键约束的核心作用是强制保持数据的**引用完整性**，确保从表（`orderitems`）中的外键值必须在主表（`orders`）中有对应的有效值 。

​	因此，正确的操作顺序必须是：先在 `orders`表中创建订单记录，获取其 `orderid`后，才能使用这个有效的 `orderid`向 `orderitems`表插入对应的订单物品记录 。这样可以有效避免数据中出现“孤儿”记录，保证关联查询结果的正确性 。

**情景：使用NEW和OLD虚拟表==验证==更新数据的有效性。**

> 维护公司的员工薪资表 `employees`。为了确保数据逻辑正确，规定**任何员工的薪资更新后，新工资 (`new_salary`) 不得低于其原工资 (`old_salary`)**。

```MySQL
CREATE TRIGGER salary_check BEFORE UPDATE
ON employees FOR EACH ROW
BEGIN
	IF NEW.salary < OLD.salary THEN
		SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT 'Salary is too low!';
	END IF;
END;
```

**情景：通过触发器==实现==库存数量自动扣减和预警。**

> 在一个电商系统数据库中，有商品库存表 `products`和订单明细表 `order_details`。为了实时保持库存准确性，并当库存过低时自动预警，请创建触发器实现以下自动化流程。

```MySQL
CREATE TRIGGER stock_alarming BEFORE INSERT
ON order_details FOR EACH ROW
BEGIN
	UPDATE products
	SET products.quantity = products.quantity - NEW.quantity
	WHERE products.prod_id = NEW.prod_id;
	UPDATE products
	SET products.is_low_stock = 'TRUE'
	WHERE products.quantity <= 10;
END;
```

**情景：创建复合触发器==处理==订单状态变更的连锁业务逻辑。**

> 订单状态的变更通常伴随着一系列连锁业务操作。假设订单主表 `orders`的状态 (`status`) 从 **‘已付款’** 变更为 **‘已完成’** 时，系统需要自动执行以下两个操作：
>
> 1. 将完成订单的商品数量累加到对应商品的 **总销量** (`total_sold`) 字段中。
> 2. 为下单用户增加相应的 **积分** (`points`)。

```MySQL
CREATE TRIGGER increase_sales_amount 
BEFORE UPDATE ON orders FOR EACH ROW
BEGIN
	UPDATE products SET total_sold = total_sold + NEW.quantity
	WHERE products.prod_id = NEW.prod_id;
	DECLARE order_amount DECIMAL(10,2) DEFAULT 0;
	SELECT NEW.prod_quantity*NEW.prod_unit_price INTO arder_amount;
	UPDATE customers AS c SET c.points = c.points + order_amount/10 ;
END;
```

**情景：使用触发器==禁止==在非工作时间修改核心业务数据。**

> 为了保障公司核心业务数据（如 **`financial_records`财务记录表**）在非工作时段（**晚上6点到次日8点**）不被意外修改，需要建立一个数据“安全锁”。

```MySQL
CREATE TRIGGER data_safety_lock BEFORE UPDATE
ON financial_records FOR EACH ROW
BEGIN
	IF (TIME() BETWEEN 18:00:00 AND 23:59:59) 
	OR (TIME() BETWEEN 00:00:00 AND 08:00:00)
		SIGNAL SQLSTATE '45000'
		SET MESSAGE_TEXT 'Modifing data is not allowed.';
	END IF;
END;
```

#### 第二十六章 管理事务处理练习题 ####

**情景：使用事务==保证==订单和订单项的同时插入或回滚。**

```MySQL

CREATE PROCEDURE CreateOrderWithItems()
BEGIN
    -- 声明变量用于错误处理和流程控制
    DECLARE success BOOLEAN DEFAULT TRUE;
    DECLARE error_message VARCHAR(255) DEFAULT '';

    -- 开始事务[3,4](@ref)
    START TRANSACTION;

    -- 1. 插入主订单记录
    INSERT INTO orders (order_number, customer_id, amount) 
    VALUES ('ORD202502061200001', 1001, 299.00);

    -- 检查订单插入是否成功
    IF ROW_COUNT() = 0 THEN
        SET success = FALSE;
        SET error_message = 'Failed to insert order record.';
    END IF;

    -- 获取刚插入的订单ID
    SET @last_order_id = LAST_INSERT_ID();

    -- 2. 逐条插入订单明细，并检查每条是否成功
    IF success THEN
        -- 插入第一条订单明细
        INSERT INTO order_items (order_id, product_id, quantity, price) 
        VALUES (@last_order_id, 501, 2, 99.50);
        IF ROW_COUNT() = 0 THEN
            SET success = FALSE;
            SET error_message = 'Failed to insert first order item.';
        END IF;
    END IF;

    IF success THEN
        -- 插入第二条订单明细
        INSERT INTO order_items (order_id, product_id, quantity, price) 
        VALUES (@last_order_id, 502, 1, 100.00);
        IF ROW_COUNT() = 0 THEN
            SET success = FALSE;
            SET error_message = 'Failed to insert second order item.';
        END IF;
    END IF;

    -- 3. 根据所有操作的成功与否决定提交或回滚[5](@ref)
    IF success THEN
        COMMIT;
        SELECT 'Order and all items inserted successfully.' AS result;
    ELSE
        ROLLBACK;
        SELECT CONCAT('Transaction rolled back: ', error_message) AS result;
    END IF;

END;

-- 调用存储过程执行事务
CALL CreateOrderWithItems();
```

**情景：通过保留点==实现==多步骤数据导入的部分回滚能力。**

```MySQL
-- 产品表
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    stock_quantity INT NOT NULL,
    category VARCHAR(50)
);

START TRANSACTION;

-- 步骤1: 导入基础产品信息 (假设此步骤必须成功)
INSERT INTO products (product_name, stock_quantity, category) 
VALUES ('新款T恤', 100, '服装');

-- 设置保存点 after_step1
SAVEPOINT after_step1;

-- 步骤2: 执行一个可能失败的数据更新操作 (例如，库存调整逻辑复杂)
UPDATE products SET stock_quantity = stock_quantity - 10 WHERE product_name = '新款T恤';

-- 检查步骤2是否成功 (这里用受影响行数模拟，实际应用需更严谨判断)
IF ROW_COUNT() = 0 THEN
    -- 步骤2失败，回滚到保存点 after_step1，即撤销UPDATE操作，保留INSERT操作
    ROLLBACK TO SAVEPOINT after_step1;
    -- 可选择执行其他补救措施或记录日志
END IF;

-- 步骤3: 继续其他操作...
-- INSERT INTO ... 等其他逻辑

-- 最终提交事务（保存点之前的操作和保存点之后未回滚的操作将被提交）
COMMIT;
```

**情景：利用事务==确保==库存检查和扣减的原子性执行。**

```MySQL
CREATE PROCEDURE stock_check
(
	IN prod_id INT,
    IN prod_demand INT
)
BEGIN
    DECLARE curr_stock INT DEFAULT 0;
	START TRANSACTION;
	UPDATE product_inventory AS pi 
	SET prod_stock = prod_stock - curr_stock
	WHERE pi.prod_id = prod_id;
	SELECT prod_stock FROM pi
	WHERE p_i.prod_id = prod_id INTO curr_stock;
	IF curr_stock < 0 THEN
		ROLLBACK;
	ELSE
		COMMIT;
	END IF;
END;
		
```



































