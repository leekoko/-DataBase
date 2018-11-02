# PL/SQL  

Z：PL/SQL是属于Oracle数据库的SQL语言。 

### 1.Oracle建表

Z：字段说明如下：

| **数据类型**     | **类型解释**                                                 |
| ---------------- | ------------------------------------------------------------ |
| VARCHAR2(length) | 可变长度：存储多少算多少。length:是最大长度，不可超过4000字节（默认1）。（1汉字=2英文=2字节） |
| CHAR(length)     | 固定长度：存再少也算length。最大长度不超过2000，效率比 VARCHAR2好一点。 |
| NUMBER(a,b)      | 整数或浮点数：a整数+点的位数，b小数的位数。如：number(6,2)存123.12345，实际：123.12 。number(4,2)存12312.345，则报错超出精度。 |
| DATA             | 时间类型：包括年、月、日、时、分、秒。                       |
| TIMESTAMP        | 时间类型：日期和时间 + 时区。内置函数systimestamp获取的就是timestamp类型 。 |
| CLOB             | 大字段类型：字段大于4000长度的字符串都能存储。               |
| BLOB             | 二进制类型：如图片、视频、声音等转换的二进制对象             |

### 2.create table语句

Z：Demo如下

```sql
-- Create table
create table STUDENT.stuinfo
(
  stuid      varchar2(11) not null,--学号：'S'+班号（7位数）+学生序号（3位数）(1)
  stuname    varchar2(50) not null,--学生姓名
  sex        char(1) not null,--性别
  age        number(2) not null,--年龄
  classno    varchar2(7) not null,--班号：'C'+年级（4位数）+班级序号（2位数）
  stuaddress varchar2(100) default '地址未录入',--地址 (2)
  grade      char(4) not null,--年级
  enroldate  date,--入学时间
  idnumber   varchar2(18) default '身份证未采集' not null--身份证
)
tablespace USERS --(3)
  storage		-- 存储参数
  (
    initial 64K    --  一次扩展64k
    minextents 1	-- 最小区段1
    maxextents unlimited	-- 最大区段无限
  );
-- Add comments to the table 
comment on table STUDENT.stuinfo --(4)
  is '学生信息表';
-- Add comments to the columns 
comment on column STUDENT.stuinfo.stuid -- (5)
  is '学号';
comment on column STUDENT.stuinfo.stuname
  is '学生姓名';
...
```

### 3.添加约束

Z：常用约束Demo如下

```sql
-- Create/Recreate primary, unique and foreign key constraints 
alter table STUDENT.STUINFO
  add constraint pk_stuinfo_stuid primary key (STUID);
  --把stuid当做主键，主键字段的数据必须是唯一性的（学号是唯一的）
   
-- Create/Recreate check constraints 
alter table STUDENT.STUINFO
  add constraint ch_stuinfo_age
  check (age>0 and age<=50);--给字段年龄age添加约束，学生的年龄只能0-50岁之内的
   
alter table STUDENT.STUINFO
  add constraint ch_stuinfo_sex
  check (sex='1' or sex='2');
   
alter table STUDENT.STUINFO
  add constraint ch_stuinfo_GRADE
  check (grade>='1900' and grade<='2999');
```

### 4.查询

#### 查询备份

Z：普通的查询很简单，这里讲快速备份查询，查询之后会生成一个新表：

```sql
create table testback as select ...
```

#### 连接操作符查询

M：如果要查询处理后的列结果，用什么实现呢？

Z：使用表达式操作查询字段，例如要将查询的多列内容合并起来，如下

```sql
select t.CNAME ||  '的登录名是：' || t.LOGIN_NAME as "NAME_LOGIN_NAME" from zc3_person t
```

【注意】``"NAME_LOGIN_NAME"``不要添加&等特殊符号，并且别名不支持单引号。   

#### 截取函数Substr

Z：如果要截取字段的某一段内容，可以使用Substr函数

```sql
select Substr(cname,1,4) as "subName" from zc3_person t
```

#### 去重函数distinct

 Z：使用``select distinct(cname) from zc3_person t  ``查询出一列中没有重复字段值。如果查询多个列，如``select distinct(cname),sex from zc3_person t   ``,查询出的cname,sex属于一个整体，需要整体相同才会排除相同的列。   

#### NULL值排序  

M：在排序时如果有Null值，我要怎么控制该数据在头还是在尾？

Z：可以使用NULLS FIRST/LAST

```sql
select * from zc3_person order by IDCARD DESC NULLS FIRST
```

#### HAVING和GROUP BY的关系

Z：GROUP BY是组查询，而HAVING相当于GROUP BY子句中的where筛选，示例如下

```sql
select cname, avg(registry)
  from zc3_person
 group by cname, registry
having avg(registry) < 4
```

当使用GROUP BY时，没法直接通过where进行带分组函数的参数筛选``avg(registry) < 4``。而HAVING 可以实现分组函数参数的筛选。   

#### 子查询返回多行的比较

M：如果我查询到多行数据，要和另外多行数据进行比较筛选，怎么实现呢？

Z：用``比较符< = > + ANY / SOME / ALL`` 实现

- ANY，SOME：满足查询结果的任何一个，等同于``< = >``最小值
- ALL：满足所有的结果，等同于``< = >``最大值

M：所以不需要用到该关键字，算出最大/最小值，再进行比较更加方便   

### 5.插入

Z：普通的insert语句如下：

```sql
insert into STUDENT.STUINFO (STUID, STUNAME, SEX, AGE, ENROLDATE)
values ('SC201801005', '龙七', '1', 26, to_date('01-09-2018', 'dd-mm-yyyy'));
```

也可以使用``INSERT INTO 表 SELECT 子句``插入结果集：

```sql
insert into student.stuinfo select * from student.stuinfo_2018;
```

### 6.连接查询  

#### 内连接

M：什么是内连接呢？

Z：内连接也称为简单连接，我们一般使用的就是内连接

```sql
select t.*, a.*
  from zc3_company_finance t, zc3_company a
 where a.code = t.apply_code
```

它的作用效果跟inner的语法是一样的

```sql
select t.*, a.*
  from zc3_company_finance t inner join zc3_company a
 on a.code = t.apply_code
```

内连接查询，两个表之间没有主从关系，其关联字段两者只要有其中一个不存在，跟其相关的两边数据都不会再查询中显示出来。

M：什么是自连接？

Z：所谓的自连接就是表通过自身字段关联自己，得出自身的特殊数据。像查询本表中相同数量但是不同类型的数据，可以用自连接来实现。（这些情况可能用group by也可以实现） 

#### 外连接   

M：左外连接，右外连接，全连接有什么区别呢？

Z：区别如下：

1. 左外连接：左表的数据全部显示，右表对应不上的列为空
2. 右外连接：右表的数据全部显示，左表对应不上的列为空  
3. 全外连接FULL  JOIN：双表数据全部显示，相当于 左外 ∪ 右外（内连接则是 左外∩右外）  









 











###