# MySql的基础  

## 1.环境开启

1. 启动mysql服务  
2. 使用navicat来登录mysql  

## 2.基础语法

1. 创建数据库：``create database databaseName;``  
2. 删除数据库：``drop database databaseName;``  
3. 创建数据表：``create table t3(a1 double, a2 float not null)``

## 3.Mysql数据类型

1. 整数，int  
2. 小数，decimal(定点数)，float4，double8  
3. 日期：date精确到日，time精确到秒，datetime时间+秒  
4. 文本：text理论无穷  
5. 二进制：bit    

类型的应用：

![p5](../img/p5.png)  

## 4.数据库设计步骤

#### 1.数据分析    

分析需要什么表，什么字段  

![p3](../img/p3.png)  

#### 2.概要设计    

建立ER图  

![p3](../img/p4.png)   

利用范式进行审核，范式越高冗余越小：  

1. 第一范式：1NF，要求每一列都有原子性（不可再分割）    
2. 第二范式：要求每个表描述一件事情（对象思想）  
3. 第三范式：表中不存在冗余字段   

#### 3.详细设计      

## 5.约束  

保证数据的正确  

#### 1.主键约束    

保证实体的完整性，该列不会出现重复值，空值  

``create table users(user_qq varchar(20) not null primary key)``  

#### 2.外键约束  

保证引用完整性  

引用其他表的列：``references users(user_qq)``,引用users表的user_qq列  

#### 3.检查约束  

保证域的完整性：``gno int not null CHECK(gno>0)``  

#### 4.默认约束  

保证域完整性，给一个缺省值``user_sex char(2) not null default '男'``    

（自增列：保证实体完整性``gno int not null AUTO_NCREMENT``）  

## 6.数据表管理  

### 1.查看数据表  

#### 1.查看数据表的基本结构    

``desc users``  

#### 2.查看表的详细结构  

``show create table table_name``可以查看创建表的sql语句  

### 2.修改数据表  

#### 1.修改表名  

``alert tabel oldName rename to newName``  

#### 2.修改字段名  

``alert table table_name CHANGE old_name new_name new_type``  

#### 3.修改字段数据类型  

``alert table table_name MODIFY col_name new_type``  

  ### 3.字段操作  

#### 1.添加字段    

``alert table table_name ADD new_col_name new_type ``  

#### 2.删除字段  

``alert table table_name DROP col_name``  

### 4.增补约束  

#### 1.增加主键约束  

``alert table table_name ADD CONSTRAINT con_name PRIMARY KEY(col_name)``  

con_name表示约束的名称,形式为：约束_ 表名 _列 ``ck _ games _gno``  

#### 2.添加外键约束  

``alert table table_name ADD CONSTRAINT con_name FOREIGN KEY(col_name) REFERENCES m_table(m_col)``    

#### 3.添加检查约束  

``alert table table_name ADD CONSTRAINT con_name CHECK(col_name>0)``    

#### 4.添加默认值  

``alert table table_name alert col_name set default value ``  

 #### 5.添加自增列  

``alert table table_name MODIFY COLUMN col_name int not null AUTO_INCREMENT primary key``  

### 5.删除数据表

#### 1.删除无关联数据表  

``DROP table table_name,table_second``  

#### 2.删除有关联数据表  

删除有关联的数据表需要先解除关联关系，再删除  

1. 解除关联关系  

``alert table table_name DROP FOREIGN KEY con_name``  

2. 删除表  

``DROP table table_name``  

## 7.数据管理  

### 1.插入数据  

1. 所有列都插入值  

``insert into table_name values ('列1','列2','列3')``  

数值类型的值不需要单引号，into可以省略  

如果该列要用默认值，就写成default  

2. 为特定列插入值  

``insert into table_name (col1,col2,col3) values (v1,v2,v3)``  

3. 一次性插入多条数据  

``insert into table_name (col1,col2,col3) values (v1,v2,v3),(v4,v5,v6)``  

### 2.修改数据  

1. 修改所有的值  

``update table_name set col_name='修改的值'``  

把所有的分数增加100分  

``update table_name set score=score+100``  

2. 修改特定数据  

``update table_name set col_name='修改的值' where col_name='指定的列'``  

### 3.删除数据  

1. 删除指定的信息  

``delete from table_name where col_name='指定的内容'``  

from可以省略  

2. truncate table清空数据  

``truncate table tabel_name``  

truncate不可以指定范围，只能清空  

## 8.数据查询  

### 1.select限制    

select语句的执行顺序：查询列-from 数据源-where筛选-group by分组-聚合统计-having再次筛选-order by排序  

#### 1.查询所有行列  

``select * from table_name``  

#### 2.查询部分列    

``select col_name1,col_name2 from table_name``  

使用别名,as可以省略  

``select col_name1 as 'new name1',col_name2 as 'new name2' from table_name``  

#### 3.查询中消除重复行  

``select DISTINCT col_name from table_name``  

#### 4.指定显示的范围  

``select * from table_name LIMIT 2,3``  

表示查询第3，4，5三条数据，脚标从2开始，当省略前面的2，表示从头开始显示三条数据  

#### 5.添加普通条件where  

``select * form table_name where col_name>10``  

多重条件:  

``select * from table_name where clo_name1=1 AND col_name>4000``  

其他的逻辑运算符还有：AND,OR,NOT  

#### 6.查询范围  

- ``select * from table_name where clo_name1>=2500 AND col_name_1<=3000``  
- 模糊查询：  ``select * from table_name where clo_name1 between 2500 and 3000``  

2500和3000不能颠倒  

查询不在这个范围内，在between前面添加not  

当要查询的范围为date日期的话：  

``select * from table_name where clo_name1 between '1991-01-01' and '2000-3-21'``  

#### 7.模糊查询  

- 通配符：  

_任意字符  

%任意长度  

[1-5]在范围1-5之内  

[^1-5]不在1-5范围之内  

- ``select * from table_name where col_name LIKE '孙%' ``  

如果查询非孙姓，在like前面加not  

#### 8.查询控制信息  

``select * from table_name where col_name IS NULL``  

查询非空就是在IS后面加NOT  

### 2.查询结果排序    

#### 1.指定列排序  

确定排序依据，排序方式  

``select * from table_name ORDER BY col_name ASC/DESC``

asc升序为默认排序，可以不添加，还可以添加where限制  

#### 2.多列进行排序  

确定多个排序依据，排序方式，排序优先级  

``select * from table_name ORDER BY col_name1 ASC,col_name2 DESC``  

挡在前面的列优先级更高  

### 3.查询处理    

#### 1.聚合函数  

sum():总和  

avg():平均值  

min(),max():求最值，日期的话2017-01-01>2007-01-1,字母的话apple<bananer  

count():计数  

1. 聚合函数的使用：  

``select count(col_name) from table_name``  

可以添加别名，添加where条件限制：

``select SUM(col_name) AS '总分' from table_name where col_name='限制条件'``  

多个聚合函数的使用：

``select SUM(col_name) AS '总分'，AVG(col_name2) AS '平均分',MAX(col_name2) AS '最高分' from table_name where col_name='限制条件'``  

（只查询一个对象）  

#### 2.结果集分组  

分组就不止产生一条数据，而是每个小组产生一条数据  

1. 使用方式:在最后再上group by col_name,以该列相同的名字进行分组  

``select col_name AS '用户名'，SUM(col_name) AS '总分'，AVG(col_name2) AS '平均分',MAX(col_name2) AS '最高分' from table_name group by col_name``  

（查询好几组对象）  

再添加降序排序的话在后面添加ORDER BY AVG(score) DESC  

2. 查询结果作为筛选条件HAVING  

having是用来筛选聚合函数结果  

``select col_name AS '用户名'，SUM(col_name) AS '总分'，AVG(col_name2) AS '平均分' from table_name group by col_name HAVING AVG(col_name)>4000``   

### 4.连接查询  

#### 1.多表查询：

``select col_name AS '用户名'，SUM(col_name) AS '总分'，AVG(col_name2) AS '平均分' from table_1,table_2,table_3 where table1.col_name=tabel2.col_name AND table2.col_name=table3.col_name ``   

form多个表，where中连接表之间的关系  

#### 2.内连接改写：  

from多个表就是内连接，还是隐式内连接  

显式内连接就是把符号显示出来  

``select col_name AS '用户名'，SUM(col_name) AS '总分'，AVG(col_name2) AS '平均分' from table_1 INNER JOIN table_2 ON table1.col_name=tabel2.col_name INNER JOIN table_3 ON table2.col_name=table3.col_name `` 

还可以添加分组  

#### 3.外连接 

分为左外连接和右外连接两种，外连接两张表并不平等  

基础表的数据必须全部出现，另一个表不存在就用null补，内连接没有数据内容，整条数据就不会显示    

``select col_name form table1_name U LEFT/RIGHT OUTER JOIN table2_name ON U.col=table2.col``  

这里的U就是设置别名  

#### 4.子查询  

1. IN子查询

将查询的结果作为另一个查询的条件  

``select * from table1_name where col_name IN (select col_name from table2.name where col2_name='限制的内容') ``  

2.  Exist子查询  

``select * from table1_name where EXIST (select * from table_name where col_name='限制条件')   ``  

只有当条件符合的时候，才会执行父查询，相当于if   

#### 5.联合查询  

将两条查询语句联合起来，形成一条新的查询语句，将结果集纵连接在一起    

``select * from table1_name where col_name='限制内容' UNION select * from table2_name where col_name IS NULL``   

当我们使用select后面加字符串的话，查到的都是字符串，当使用union的时候，重复的内容不会显示    





   



  

















(看到_MYSQL函数_，loading）