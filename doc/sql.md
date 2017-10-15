# sql语法   

## 1.  基础语句（feiman）   
### 1.重要命令  
- SELECT - 从数据库中提取数据
- UPDATE - 更新数据库中的数据
- DELETE - 从数据库中删除数据
- INSERT INTO - 向数据库中插入新数据
- CREATE DATABASE - 创建新数据库
- ALTER DATABASE - 修改数据库
- CREATE TABLE - 创建新表
- ALTER TABLE - 变更（改变）数据库表
- DROP TABLE - 删除表
- CREATE INDEX - 创建索引（搜索键）
- DROP INDEX - 删除索引 

### 2.DISTINCT 语法   
查询列中的元素 减去 重复元素 
```xml
SELECT DISTINCT column_name,column_name
FROM table_name;
```

### 3.insert语句   
也可以不指定列进行插入   
```xml
INSERT INTO Websites (name, url, country)
VALUES ('stackoverflow', 'http://stackoverflow.com/', 'IND');
```
### 4.update语句
（注意where）
```xml
UPDATE Websites 
SET alexa='5000', country='USA' 
WHERE name='菜鸟教程';
```

### 5.delete语句   
删除指定的行（注意where）
```xml
DELETE FROM Websites
WHERE name='百度' AND country='CN';
```

### 6.TOP, LIMIT语句
限定查询的条数，对大记录表常用   
1. Mysql的limit：``SELECT * FROM Persons LIMIT 5;``  
2. Oracle的limit:``SELECT * FROM Persons WHERE ROWNUM <=5;``  

3. top语句：``SELECT TOP 50 PERCENT * FROM Websites;``   

### 7.like语句   
模糊搜索，下面是不包含语句   
``SELECT * FROM Websites WHERE name NOT LIKE '%oo%';``  

- ”_“单个字符通配符的使用 ：
```xml
SELECT * FROM Websites
WHERE name LIKE 'G_o_le';
```
-  [charlist] 通配符  
  查询以A到H任何一个字符开头的信息，也可以写成``^[ABC]`` 表示以ABC其中一个字符开头  
```xml
SELECT * FROM Websites
WHERE name REGEXP '^[A-H]';   
```

### 8.in语法   
相当于where，但是in可以一次规定多个值   
```xml
SELECT * FROM Websites
WHERE name IN ('Google','菜鸟教程');
```
转化成where就是``where name='Google' or name='菜鸟教程'``   

### 9.between语法   
```xml
SELECT * FROM access_log
WHERE date BETWEEN '2016-05-10' AND '2016-05-14';
```
between可以对日期进行筛选，输出的值包括2016-05-10和2016-05-14，不包括between前面添加not   

###10.as别名   
```xml
SELECT name, CONCAT(url, ', ', alexa, ', ', country) AS site_info
FROM Websites;
```
之中CONCAT将几个列组合成一个列，再给它设置别名   

###11.join语法
 - INNER JOIN：如果表中有一个不匹配，则不返回行   
 - LEFT JOIN：即使右表中没有匹配，也从左表返回所有的行   
 - RIGHT JOIN：即使左表中没有匹配，也从右表返回所有的行   
 - FULL JOIN：只要其中一个表中存在匹配，则返回行

```
[TEST@ORA1] SQL>select * from a;

编号 姓名
---- ----------
1000 张三
2000 李四
3000 王五

[TEST@ORA1] SQL>select * from b;

编号 商品
---- ----------
1000 电视机
2000 录像机
4000 自行车

[TEST@ORA1] SQL>set null 空值--这里为了显示方面我把NULL定义成了[空值]

[TEST@ORA1] SQL>select a.*,b.* from a inner join b on a.编号=b.编号;

编号 姓名       编号 商品
---- ---------- ---- ----------
1000 张三       1000 电视机
2000 李四       2000 录像机

[TEST@ORA1] SQL>select a.*,b.* from a left join b on a.编号=b.编号;

编号 姓名       编号 商品
---- ---------- ---- ----------
1000 张三       1000 电视机
2000 李四       2000 录像机
3000 王五       空值 空值

[TEST@ORA1] SQL>select a.*,b.* from a right join b on a.编号=b.编号;

编号 姓名       编号 商品
---- ---------- ---- ----------
1000 张三       1000 电视机
2000 李四       2000 录像机
空值 空值       4000 自行车

[TEST@ORA1] SQL>select a.*,b.* from a full join b on a.编号=b.编号;

编号 姓名       编号 商品
---- ---------- ---- ----------
1000 张三       1000 电视机
2000 李四       2000 录像机
3000 王五       空值 空值
空值 空值       4000 自行车
```
（以上数据内容引自一位网友的博客，由于名字，所以没把名字加上去，可联系我添加博客链接）   

###12.UNION操作符   
UNION 中每个 SELECT 语句必须数量相同，数据类型相似，顺序相同。
```xml
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```
如果想要显示值相同的数据，则用UNION ALL  

###13.SELECT INTO复制
SELECT INTO 语句从一个表复制数据，然后把数据插入到另一个新表中。 
1. 新建复制整个表  :``CREATE TABLE 新表 SELECT * FROM 旧表 ``  
2. 新建复制指定列：
```xml
CREATE TABLE 新表 SELECT * FROM 旧表 
```
3.新建复制表不包括内容：
```xml
SELECT *
INTO newtable
FROM table1
WHERE 1=0;
```
4.复制已有表内容   
```xml
INSERT INTO table2
SELECT * FROM table1;
```
复制已有表内容的指定列
```xml
INSERT INTO Websites (name, country)
SELECT app_name, country FROM apps;
```

### 14.sql约束
- NOT NULL - 指示某列不能存储 NULL 值。
- UNIQUE - 保证某列的每行必须有唯一的值。
  _每个表可以有多个 UNIQUE 约束，但是每个表只能有一个 PRIMARY KEY 约束。_  
- PRIMARY KEY - NOT NULL 和 UNIQUE 的结合。确保某列（或两个列多个列的结合）有唯一标识，有助于更__容易更快速地找到表中的一个特定的记录。_
- FOREIGN KEY - 保证一个表中的数据匹配另一个表中的值的参照完整性。


_设置一个字段是另一个表的外键可以预防破坏表之间连接的行为_  

- CHECK - 保证列中的值符合指定的条件。  


_check约束用来定义一些值的规则_   

```xml
ALTER TABLE Persons
ADD CHECK (P_Id>0)
```

_P_Id的输入值必须大于0_    

- DEFAULT - 规定没有给列赋值时的默认值。  


_当值为空的时候，使用默认值_

```xml
ALTER TABLE Persons
ALTER City SET DEFAULT 'SANDNES'
```

### 15.索引   

在 "Persons" 表的 "LastName" 列上创建一个名为 "PIndex" 的索引：

```xml
CREATE INDEX PIndex
ON Persons (LastName)
```

如果要创建唯一的索引值，再index前面添加unique  

### 16.删除语句   

- 删除索引  

mysql：``ALTER TABLE table_name DROP INDEX index_name``  

oracle:``DROP INDEX index_name``  

- 删除表

``DROP TABLE table_name``  

- 删除数据库  

``DROP DATABASE database_name``  

- 清空表内数据   

``TRUNCATE TABLE table_name``    

### 17.视图   













看到http://www.runoob.com/sql/sql-view.html

