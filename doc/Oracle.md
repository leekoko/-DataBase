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

### 5.Oracle导出导入  

#### Imp方式导入导出   

Z：导出命令

```java
exp oa_db/11@192.168.0.176/orcl file=D:\oa_db.dmp owner=oa_db;
```

导入命令

```java
imp oa_db/11@192.168.0.176/tech file=D:\oa_db.dmp full=y ignore=y;
```

