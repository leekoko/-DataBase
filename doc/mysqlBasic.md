# MySql的基础  

### 1.环境开启        

1. 启动mysql服务  
2. 使用navicat来登录mysql  

### 2.基础语法  

1. 创建数据库：``create database databaseName;``  
2. 删除数据库：``drop database databaseName;``  
3. 创建数据表：``create table t3(a1 double, a2 float not null)``

### 3.Mysql数据类型  

1. 整数，int  
2. 小数，decimal(定点数)，float4，double8  
3. 日期：date精确到日，time精确到秒，datetime时间+秒  
4. 文本：text理论无穷  
5. 二进制：bit    

### 4.数据库设计步骤  

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





(看到为列添加约束，loading)