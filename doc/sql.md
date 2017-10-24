# sql语法   

## 1.  基础语句   
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

2. Oracle的limit:``SELECT * FROM Persons WHERE ROWNUM <11;``     

   查询前10条数据       

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
  查询以A到H任何一个字符开头的信息，也可以写成``^[ABC]`` 表示以ABC其中一个字符开头  s
### 8.in语法   

相当于==，但是in可以一次规定多个值   
```xml
SELECT * FROM Websites
WHERE name IN ('Google','菜鸟教程');
```
转化成where就是``where name='Google' or name='菜鸟教程'`` ，相比之下in的效率更高      

【真实例子】

```xml
select 
	tpi.* from th_project_info tpi, th_contract_management tcm, th_project_superv tps 
where 
	tpi.code = tcm.proid and tps.projectcode = tpi.code 			 	 
and 
	tps.refen1 = '0' 				 
and 
	tpi.state > 3	 			 
and 
	tpi.code 
not in 
	(select 
		ar.PROJECTCODE 	 
	from 
		th_apply_record ar,th_project_apply tpa 	 
	where 
		ar.applycode=tpa.code 	 
	and 
		tpa.refen2 <>'1' 	 
	and 
		ar.APPLYFLAG ='2')
```

首先从tpi，tcm，tps中筛选出信息，然后排除掉包含ar，tpa筛选出来的PROJECTCODE列内容的行   

_in只要有一个符合就返回true_

（相似的有 exists ）

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

【真实例子】

```xml
select a.*,l.lock_seq as lockSeq,l.lock_remark as addMan from th_project_batch a left join th_lock l on l.lock_seq=a.code
```

以th_project_batch为主表，通过l.lock_seq=a.code连接，将主表所有的列显示出来，附带th_lock中对应的列。  

###12.UNION操作符   
UNION 中每个 SELECT 语句必须数量相同，数据类型相似，顺序相同。
```xml
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```
如果想要显示值一摸一样的数据，则用UNION ALL   

（查询交集使用intersect）   

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

- 创建视图CREATE   

```xml
CREATE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition
```

- 更新视图语句CREATE OR REPLACE   

```xml
CREATE OR REPLACE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition
```

- 删除视图   

```xml
DROP VIEW view_name
```

### 18.case，when，then语句

将查询的数字转化为对应的文字输出

```xml
select u.id,u.name,
	(case u.sex
     	when 
     		1 then '男'
        when 
        	2 then '女'
        else 
        	'空的'
        end
	)性别
from 
	users u;
```

【真实例子】

```xml
select * from 
	(select 
		t.*,t.type as projectType,
          case 
              when 
                  t.current_step is null or t.current_step ='' 
			 then 0
              when 
                  t.current_step is not null 
			 then t.current_step
          end 
              projectState 
	from 
		fhq_project_info t) a
```

查询的时候当current_step为空或者null，则显示0.否则显示原来的数据（必须），显示的列为projectState   

### 19.inst函数   

> instr(string1,string2[,start_position[,nth_appearence]])
>
> string1：要在此字符串中查找。
>
> string2：要在string1中查找的字符串。
>
> start_position：从string1开始查找的位置。可选，默认为1，正数时，从左到右检索，负数时，从右到左检索。
>
> nth_appearence：查找第几次出现string2。可选，默认为1，不能为负。
>
> 注：如果没有查找到，返回0。
>
>  例如：
>
> select instr('abcd','a') from dual;　　--返回1
> select instr('abcd','c') from dual;　　--返回3
> select instr('abcd','e') from dual;　   --返回0

【真实例子】

```xml
select rownum, ta.*
           from (select t.*,
                        tnu.noticestate1,
                        tnu.code       as usercode,
                        tnu.refen2       as throle,
                        tnu.receiveuser1 as thuser,
                        (select wm_concat(tpi.pname)
                           from th_notice_group g, th_project_info tpi
                           where instr(g.proids, tpi.code) > 0
                           and g.refen1 = t.code) as pname, t.code as tcode
                   from th_project_notice t, th_notice_user tnu
                  where t.code = tnu.refen1 and t.del_flag!=1
                    and t.noticetypt > 1
                  order by tnu.noticestate1 asc, t.sendtime desc) ta
```

查找tpi.pname，筛选它的g.proids是否包含tpi.code   

### 20.Oracle trunc()函数

/**************日期********************/

1. select trunc(sysdate) from dual --2013-01-06 今天的日期为2013-01-06
2. select trunc(sysdate, 'mm') from dual --2013-01-01 返回当月第一天.
3. select trunc(sysdate,'yy') from dual --2013-01-01 返回当年第一天
4. select trunc(sysdate,'dd') from dual --2013-01-06 返回当前年月日
5. select trunc(sysdate,'yyyy') from dual --2013-01-01 返回当年第一天
6. select trunc(sysdate,'d') from dual --2013-01-06 (星期天)返回当前星期的第一天
7. select trunc(sysdate, 'hh') from dual --2013-01-06 17:00:00 当前时间为17:35 
8. select trunc(sysdate, 'mi') from dual --2013-01-06 17:35:00 TRUNC()函数没有秒的精确

/***************数字********************/
/*
TRUNC（number,num_digits） 
Number 需要截尾取整的数字。 
Num_digits 用于指定取整精度的数字。Num_digits 的默认值为 0。
TRUNC()函数截取时不进行四舍五入
*/

9. select trunc(123.458) from dual --123
10. select trunc(123.458,0) from dual --123
11. select trunc(123.458,1) from dual --123.4
12. select trunc(123.458,-1) from dual --120
13. select trunc(123.458,-4) from dual --0
14. select trunc(123.458,4) from dual --123.458
15. select trunc(123) from dual --123
16. select trunc(123,1) from dual --123
17. select trunc(123,-1) from dual --120

【真实例子】

```xml
select a.id,a.code,a.pname,a.cuser,a.cusername,a.scope,a.cdate,a.principal,a.contact,a.famount,
      a.state,a.refen2,a.refen7,a.backopinion,a.constructioncycle,a.modify_date,
      b.supervname,b.projectcode,b.refen1,
      c.business_id,c.receive_time,
      trunc(SYSDATE - to_date(c.receive_time,'yyyy-MM-dd HH24:mi:ss'))  delay
from 
      th_project_info a,
      th_project_superv b,
	  c_task_info c
where 
	  c.receive_time < to_char(SYSDATE - 5, 'yyyy-MM-dd')
      and a.refen2 = '待评审单位初审'
      and a.abolish = '0' 
      and b.refen1='3' 
      and a.code = b.projectcode
      and a.code = c.business_id 
```

to_date() 作用将字符类型按一定格式转化为日期类型  

trunc在这里的作用就是将（系统时间-获取时间）转化为yyyy-MM-dd格式，然后作为delay参数输出。

to_char将时间按yyyy-MM-dd转化为字符串   

### 21.Group by分组   

group对部分信息的共性进行提取，一般和**分组统计函数**配合使用   

```
Count:找到记录数
Min:列的最小值  
Max:列的最大值
Sum：计算列的总和 
Avg：计算列的平均数    
```

【例子】查询每个雇员sales_rep_id总订单数   

```xml
select sales_rep_id,sum(order_total) from orders group by sales_rep_id;
```

### 22.having限制搜索条件    

having跟where类似，对查询进行设置条件。区别在于：where是先设置条件再查询，而having是查询后再进行条件筛选。   （尽量使用where）

_从苹果树上摘好的苹果（where）；把很多苹果一起摘下来，挑选好的一部分（having）_

having通常配合统计函数使用   

【例子】查询总订单数再150 000以上的统计数据   

```   xml
select sales_rep_id ,sum(order_total) 
from orders 
group by sales_rep_id  
having sum(order_total) > 150000;
```

### 23.自连接   

有时候需要将同一个表自身数据进行比较，这个时候就需要用自连接   

```xml 
SELECT FIRST.CNumber， SECOND.PCNumber
FROM Course FIRST， Course SECOND
WHERE FIRST.PCNumber=SECOND.CNumber；
```

### 24.自然连接   

自然联结就是将有相同列的两个表进行组合成一张表，对应不上的就不显示，一对二的就拆分成两条数据   

【例子】

```xml
select product_id,p.categroy_id,category_name 
from product_information p 
natural join categories ca;  
```