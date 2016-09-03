# sqlmap.conf
在分析sqlmap源码前先阅读了slqmap文件下的sqlmap.conf,sqlmap.conf中几乎包含sqlmap的所有扫描方式，可以看出阅读sqlmap.conf不管是对与分析源码还是使用sqlmap都有很大的帮助。我从sqlmap.conf中选取部分常用的参数来记录笔记。

## 参数： -dbms
 - 指定探测的服务器数据库系统，可以看出sqlmap支持探测的数据库有mssql, mysql, mysql 4, mysql 5, oracle, pgsql, sqlite, sqlite3,access, firebird, maxdb, sybase。默认情况下sqlmap会自动探测web后端的数据库类型。
 ```sh
# Force back-end DBMS to this value. If this option is set, the back-end
# DBMS identification process will be minimized as needed.
# If not set, sqlmap will detect back-end DBMS automatically by default.
# Valid: mssql, mysql, mysql 4, mysql 5, oracle, pgsql, sqlite, sqlite3,
# access, firebird, maxdb, sybase
dbms = 
```
## 参数: -u/url
 - 参数后面跟要探测的url地址。
```sh
# Target URL.
# Example: http://192.168.1.121/sqlmap/mysql/get_int.php?id=1&cat=2
url = 
```
## 参数： -l
- 使用burpsuite或是其他扫描软件生成日志，可以用-l参数将日志文件导入sqlmap中，sqlmap自动对文件进行扫描。
```sh
# Parse targets from Burp or WebScarab logs
# Valid: Burp proxy (http://portswigger.net/suite/) requests log file path
# or WebScarab proxy (http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project)
# 'conversations/' folder path
logFile = 
```
## 参数： -g
 - sqlmap可以测试注入goole搜索结果中的get参数。
```sh
# Rather than providing a target URL, let Google return target
# hosts as result of your Google dork expression. For a list of Google
# dorks see Johnny Long Google Hacking Database at
# http://johnny.ihackstuff.com/ghdb.php.
# Example: +ext:php +inurl:"&id=" +intext:"powered by "
googleDork = 
```
## 参数： -r
 - sqlmap从文本中获取http参数，这样可以省略一些参数的设置，方便有效。
```sh
# Load HTTP request from a file
# Example (file content): POST /login.jsp HTTP/1.1\nHost: example.com\nUser-Agent: Mozilla/4.0\n\nuserid=joe&password=guessme
requestFile = 
```
## 参数： --data
 - 用于post测试，将数据进行post提交，sqlmap测试post扫描post参数。
```sh
# Data string to be sent through POST.
data = 
```
## 参数： --current-user
 - 可以返回当前数据库的用户名。
```sh
# Retrieve back-end database management system current user.
# Valid: True or False
getCurrentUser = False
```
## 参数： --current-db
 - 可以返回当前连接的数据库。
```sh
# Retrieve back-end database management system current database.
# Valid: True or False
getCurrentDb = False
```
## 参数： -passwords
 - 当sqlmap读出数据库系统管理的密码时，可以列出hash来尝试破解密码
```sh
# Enumerate back-end database management system users password hashes.
# Valid: True or False
getPasswordHashes = False
```
## 参数： -v
 - 观察sqlmap对目标进行判断和读取数据的过程。
 - -v 0 只显示严重错误信息。
 - -v 1 显示基本信息和告警信息。
 - -v 2 显示debug信息。
 - -v 3 显示注入payloads。
 - -v 4 显示http请求。
 - -v 5 显示hhttp响应头。
 - -v 6 显示http响应页面。
```sh
# Verbosity level. //Verbosity 信息显示或是冗长
# Valid: integer between 0 and 6
# 0: Show only error and critical messages
# 1: Show also warning and info messages
# 2: Show also debug messages
# 3: Show also payloads injected
# 4: Show also HTTP requests
# 5: Show also HTTP responses' headers
# 6: Show also HTTP responses' page content
# Default: 1
```
## 参数 ： --level
 - sqlmap执行探测等级。等级为1-5，默认为1，当等级大于等于3时，对Agent-user进行探测。
```sh
# Level of tests to perform.
# The higher the value is, the higher the number of HTTP(s) requests are
# as well as the better chances to detect a tricky SQL injection.
# Valid: Integer between 1 and 5
# Default: 1
```
## 参数： --risk
 - sqlmap探测时的风险等级，默认为1，risk=1时测试大部分的语句，risk=2时增添基于事件的探测语句，risk=3时增加‘or’语句。
```sh
#Risk of tests to perform.
# Note: boolean-based blind SQL injection tests with AND are considered
# risk 1, with OR are considered risk 3.
# Valid: Integer between 1 and 3
# Default: 1
```
## 参数： --technique
 - 指定sqlmap的探测技术，默认会测试所有情况。
 - -B 布尔型注入
 - -E 基于错误注入
 - -U 联合查询注入
 - -T 时间注入
 - -Q 内联sql注入
```sh
# These options can be used to tweak testing of specific SQL injection
# techniques.
[Techniques]
# SQL injection techniques to use.
# Valid: a string composed by B, E, U, S, T and Q where:
# B: Boolean-based blind SQL injection
# E: Error-based SQL injection
# U: UNION query SQL injection
# S: Stacked queries SQL injection
# T: Time-based blind SQL injection
# Q: Inline SQL injection
# Example: ES (means test for error-based and stacked queries SQL
# injection types only)
# Default: BEUSTQ (means test for all SQL injection types - recommended)
tech = BEUSTQ
```
```sh
sqlmap.py -u http://xxx.xxx.xxx.xxx/?id=1 --current-db --dbms
sqlmap.py -u http://xxx.xxx.xxx.xxx/?id=1 -D A --tables
sqlmap.py -u http://xxx.xxx.xxx.xxx/?id=1 -D A -T b --columns --dump
sqlmap.py -r --current-db --dbms
```
