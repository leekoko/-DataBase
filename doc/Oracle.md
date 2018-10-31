# Oracle基本操作  

### 1.数据库连接

Z：数据库连接可以使用Oracle自带的SQL Plus工具。本地连接输入``sqlplus``即可

M：那如果要连接到远程数据库上呢？

Z：首先使用``sqlplus/nolog``启动该工具，然后输入登录命令

1. 管理员登录命令``conn sys/11@127.0.0.1:1521/orcl as sysdba``   
   - sysdba:管理员需要指定sysdba身份才可以登录，其他用户无需添加as sysdba

### 2.Oracle服务介绍

1. OracleService+服务名（ORCL）:Oracle基础，启动才能使用Oracle数据库
2. OracleOraDb11g_home1TNSlistener：监听服务，只有启动该服务，本地的客户端程序才能通过监听连接到数据库，和数据库进行交互。
3. _(非必须启动)OracleMTSRecoveryService：服务端控制。该服务允许数据库充当一个微软事务服务器MTS、COM/COM+对象和分布式环境下的事务的资源管理器。_
4. _(非必须启动)OracleOraDb11g_home1ClrAgent：Oracle数据库 .NET扩展服务的一部分。_
5. _(非必须启动)OracleJobSchedulerORCL：Oracle作业调度(定时器)服务，ORCL是Oracle实例标识。_  

### 3.Oracle用户   

1. 创建用户

   ```sql
   -- Create the user 
   create user testLyb--用户名
     identified by "123456"--密码
     default tablespace USERS--表空间名
     temporary tablespace temp --临时表空间名
     profile DEFAULT    --数据文件（默认数据文件）
     account unlock; -- 账户是否解锁（lock:锁定、unlock解锁）
   ```

   创建用户，当前的用户没有登录数据库的权限。

2. 用户权限

   - 系统权限：如create session数据库连接权限，或者create table、create view 等具有创建数据库对象权限。

     ```sql
     --GRANT 系统权限 to 用户
     grant select any table to testLyb;
     
     -- Revoke 取消系统权限  from 用户
     revoke SELECT ANY TABLE from testLyb;
     ```

     一般赋予系统权限是通过设置角色的方式，配置角色相当于配置了系统权限的集合。常用的角色如下：

     1. CONNECT角色：连接服务器，建立会话
     2. RESOURCE角色：创建自己的对象，像表、视图之类的
     3. DBA角色：管理员权限

     ```sql
     --GRANT 角色 TO 用户
     grant dba to testLyb;	--授权connect角色
     grant resource to testLyb;	--授予resource角色
     
     -- Revoke 角色（role） from 用户
     revoke RESOURCE from testLyb;
     ```

   - 对象权限：对数据库增删改查等操作

     ```sql
     --GRANT 对象权限 on 对象 TO 用户    
     grant select, insert, update, delete on JSQUSER to testLyb;
     
     -- Revoke 对象权限 on 对象 from 用户 
     revoke select, insert, update, delete on JSQUSER from testLyb;
     ```

3. 修改用户信息

   ```sql
   --修改用户信息
   alter user testLyb
     identified by 11  --修改密码
     account lock;--修改用户处于锁定状态或者解锁状态 （LOCK|UNLOCK ）
   ```

### 4.Oracle常用SQL语言概括

```sql
数据定义语言（DDL），包括 CREATE（创建）命令、 ALTER（修改）命令、 DROP（删除）命令等。

数据操纵语言（DML），包括 INSERT（插入）命令、 UPDATE（更新）命令、 DELETE（删除）命令、 SELECT … FOR UPDATE（查询）等。

数据查询语言（DQL），包括基本查询语句、 Order By 子句、 Group By 子句等。

事务控制语言（TCL），包括 COMMIT（提交）命令、 SAVEPOINT（保存点）命令、ROLLBACK（回滚）命令。     

数据控制语言（DCL）， GRANT（授权）命令、 REVOKE（撤销）命令。
```

### 5.Oracle建表

Z：字段说明如下：

| **数据类型**     | **类型解释**                                                 |
| ---------------- | ------------------------------------------------------------ |
| VARCHAR2(length) | 可变长度：存储多少算多少。length:是最大长度，不可超过4000（默认1）。 |
| CHAR(length)     | 固定长度：存再少也算length。最大长度不超过2000，效率比 VARCHAR2好一点。 |
| NUMBER(a,b)      | 整数或浮点数：a整数+点的位数，b小数的位数。如：number(6,2)存123.12345，实际：123.12 。number(4,2)存12312.345，则报错超出精度。 |
| DATA             | 时间类型：包括年、月、日、时、分、秒。                       |
| TIMESTAMP        | 时间类型：日期和时间 + 时区。内置函数systimestamp获取的就是timestamp类型 。 |
| CLOB             | 大字段类型：字段大于4000长度的字符串都能存储。               |
| BLOB             | 二进制类型：如图片、视频、声音等转换的二进制对象             |

### 6.create table语句

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

### 7.添加约束

Z：约束Demo如下

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

### 8.查询

Z：普通的查询很简单，这里讲快速备份查询，查询之后会生成一个新表：

```sql
create table testback as select ...
```

### 9.插入

Z：普通的insert语句如下：

```sql
insert into STUDENT.STUINFO (STUID, STUNAME, SEX, AGE, ENROLDATE)
values ('SC201801005', '龙七', '1', 26, to_date('01-09-2018', 'dd-mm-yyyy'));
```

也可以使用``INSERT INTO 表 SELECT 子句``插入结果集：

```sql
insert into student.stuinfo select * from student.stuinfo_2018;
```































----

oracle导出未经测试



目录对象

create directory lybDire as 'D:\lybDbFile';  

_drop directory lybDire;_  

授权

grant write,read on directory lybDire to 数据库用户



expdp 用户/密码 directory=NAME dumpfile=导出名 tables=表名；



impdp 用户/密码 directory=NAME dumpfile=导出名 tables=表名；











OEM逻辑备份

M：Oracle EM的地址是什么？

Z：https://127.0.0.1:1158/em  

M：怎么登录EM控制台呢？







