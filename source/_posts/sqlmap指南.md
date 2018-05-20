---
title:sqlmap 指南
tags: 
    - CTF
    - security
    - web
---

## sql注入介绍：

假设需要审计的网站根据get，post，cookie，user-agent生成动态网页，现需测试其是否存在sql 注入漏洞。

若`http://192.168.136.131/sqlmap/mysql/get_int.php?id=1+AND+1=1`页面内容与`http://192.168.136.131/sqlmap/mysql/get_int.php?id=1+AND+1=2`页面内容不同，则显示该页面可能存在sql注入漏洞。

基本概念：

盲注(blind injection)：无法看到测试SQL语句的回显调试信息的注入方法。

延时注入：在无法判断测试SQL语句是否被执行的情况下，利用BENCHMARK()函数判断SQL语句是否被执行，寻找注入点。

[webshell](https://malware.expert/general/what-is-a-web-shell/)：攻击者用目标网站所支持的编程语言编写的脚本，存储在目标网站服务器上，可以用来操作目标网站。

Note.
对于NoSQL，可以使用NoSQLmap进行注入。


## 注入探测

### Get请求

`sqlmap.py -u url`

### Post请求

`sqlmp.py -u url --data={key=value}`

### cookie方式

`sqlmap.py -u url --cookie={cookie}`

## 基本操作

**获取数据库名** `--current-db`

**获取用户名** `--current-user`

**获取数据量**`--count -D {databaseName}`

**获取当前用户权限**`--privileges`

**延时注入**`--delay {delayTime}`

**上传webshell**`--os-shell`

**上传本地文件到服务器**`--file-write={localFile} --file-dest={destPath}`

**利用文本文件批量检测**(https://github.com/sqlmapproject/sqlmap/wiki/Usage#scan-multiple-targets-enlisted-in-a-given-textual-file)`-m`

[**利用burp suite日志进行批量检测**](https://github.com/sqlmapproject/sqlmap/wiki/Usage#parse-targets-from-burp-or-webscarab-proxy-logs)`-l`

**verbose模式**显示攻击载荷，http请求，响应等。`-v {0-6}`

**指定代理**转发到burp suite进行攻击，`--proxy={}`


Note.

Microsoft SQL server 最高权限用户名:sa
MySQL 最高权限用户名:root

## 攻击载荷

载荷目录xml/payloads

## 测试等级

使用`--level=2` 指定等级，一般高一点。
等级为2，测试cookie。
等级为3，测试User-Agent，Referer。

## 风险等级

使用`--risk=2`指定，一般低一点。

共有3个风险等级。

 - 等级1，无风险。
 - 等级2，会执行耗时数据库操作。
 - 等级3，加入OR语句。比如Update语句中加入OR，可能导致整个数据表更改。
 
 

## SQL注入常规流程

 1. 获取库名`--current-db`
 2. 获取表名`--table -D "{databaseName}"`
 3. 获取列名`--columns -T "{tableName}" -D "{databaseName}"`
 4. 获取某列字段所有值`-C "{columnName}" -T "{tableName}" -D "{databaseName}" --dump`
 
 ## 直连自己的数据库
 
 `-d "mysql://account:password@address:port/databaseName"`
 
 打开数据库的shell:`--sql-shell`

## tamper脚本

tamper脚本支持高级的注入，如绕过WAF(web application Firewall)。

`--tamper={scriptName}`

## sqlmap目录结构

![目录结构](./images/1526699051623.jpg)


## sqlmap支持的注入技术

### 布尔值盲注

// TODO




[sql注入资料](https://del.icio.us/inquis/sqlinjection)

[sqlmap官方视频教程](https://www.youtube.com/user/inquisb/videos)

[sqlmap使用说明](https://github.com/sqlmapproject/sqlmap/wiki/Usage)

在DWVA下测试sql注入：
[DVWA安装方法](https://www.youtube.com/watch?v=5BG6iq_AUvM)