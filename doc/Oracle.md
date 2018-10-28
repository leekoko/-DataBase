





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







