# xml文件夹
包含banner文件夹\errors.xml\livetests.xml\payloads.xml\phpids_rules.xml\queries.xml

## banner文件夹
 - 在banner文件夹中，有cookie、generic（服务器类型）、mssql/mysql、oracle、postgresql、servlet（java编写的服务器端程序）
、sharepoint（Microsoft Share Point）以及http头中的x-aspnet-version和x-powered-by。

## payloads.xml
 - payloads.xml是sqlmap在进行sql注入测试时使用的payloads，如：
 - AND "[RANDSTR]"="[RANDSTR] AND语句
 - OR NOT [RANDNUM]=[RANDNUM1] OR语句
 
## phpids_rules php检测系统的正则。
 

#### queries.xml
<root>为xml文件的根节点,<root>根节点包含11个<dbms>节点，由<dbms>
节点的value可以看出，每一个<dbms>节点代表一个sqlmap支持的数据库。MySQL、PostgreSQL、
Microsoft SQL Server、Oracle、SQLite、Microsoft Access、Firebird、SAP MaxDB、Sybase、
IBM DB2、HSQLDB。而且每一个元素节点中都有一个query属性值。可以看出sqlmap是根据用户在
shell中输入的命令或是参数来xml文件中选择相应的query属性值。

我们选择常见的MySQL、Microsoft SQL Server、Oracle、PostgreSQL5中数据库，来比较
分析各自的query属性值。

#### cast()函数（转换类型函数）是源值转化为目标类型，AS关键字将源值%S和目标类型CHAR分隔，
可以看到4中表达式的字符类型是不一样的，在PostgreSQL中文文档中可以看到“char(n) 和 varchar(n) 
的概念分别是 character(n) 和 character varying(n) 的别名， 没有长度声明词的character 等于 character(1)”
 - MYSQL <cast query="CAST(%s AS CHAR)"/>
 - PostgreSQL <cast query="CAST(%s AS CHARACTER(10000))"/>
 - Microsoft SQL Server <cast query="CAST(%s AS NVARCHAR(4000))"/>
 - Oracle<cast query="CAST(%s AS VARCHAR(4000))"/>

#### 获取源值的字符串长度 CHAR_LENGTH(%S)计算源值的字符长度，多字节字符算一个单独的字符；而在LENGTH()
中数字和字母是一个字符，汉字是三个字符长度。SQLserver中LTRIM()函数是出去字符串头部空格，STR()函数是返回一个
数字的等效字符串，LEN()函数是返回文本字段中值得长度。
 - MYSQL <length query="CHAR_LENGTH(%s)"/>
 - PostgreSQL <length query="LENGTH(%s)"/>
 - Microsoft SQL Server <length query="LTRIM(STR(LEN(%s)))"/>
 - Oracle  <length query="LENGTH(%s)"/>
 
#### 源值不为NULL，返回源值，否则返回NUL。
而返回的类型是数字和是字符串值，取决去它的上下文环境；sql server中的ISNULL()
函数要求前后数据类型一样；PostgreSQL中的COALESCE()函数依次执行表达式，直到遇到非NULL值即停止并返回该值，如果均为NULL，
返回空值；Oracle数据库中的NVL()函数格式是NVL( string1, replace_with)，如果string1为NULL返回replace_with，如果两个值均
为NULL，返回NULL值。
 - MYSQL <isnull query="IFNULL(%s,' ')"/>
 - PostgreSQL <isnull query="COALESCE(%s,' ')"/>
 - Microsoft SQL Server  <isnull query="ISNULL(%s,' ')"/>
 - Oracle <isnull query="NVL(%s,' ')"/>
 
#### 数据库的定界符
 - MySQL <delimiter query=","/>
 - PostgreSQL <delimiter query="||"/>
 - Microsoft SQL Server  <delimiter query="+"/>
 - Oracle  <delimiter query="||"/>
 
#### LIMIT()用于强制select语句返回指定的记录数；PostgreSQL 8.1 中文文档中可以看到“附加上 LIMIT和OFFSET 子句之后，你就可以检索
原来查询语句查询出来的结果中的一部分数据行”，SELECT TOP用于返回指定的记录数；Oracle数据库中的ROWNUM()函数同样是用于分页。
 - MySQL <limit query="LIMIT %d,%d"/>
 - PostgreSQ <limit query="OFFSET %d LIMIT %d"/>
 - Microsoft SQL Server <limit query="SELECT TOP %d "/>
 - Oracle <limit query="ROWNUM AS LIMIT %s) WHERE LIMIT"/>

#### ORDER BY语句用于对结果集进行排序。
 - MySQL <order query="ORDER BY %s ASC"/>
 - PostgreSQL  <order query="ORDER BY %s ASC"/>
 - rosoft SQL Server <order query="ORDER BY %s ASC"/>
 - Oracle  <order query="ORDER BY %s ASC"/>

#### COUNT()函数是返回匹配条件的行数。
 - MySQL、Microsoft SQL Server、Oracle、PostgreSQL <count query="COUNT(%s)"/>

#### MYSQL数据库中的MID()函数用于得到字符串的一部分，Oracle、Microsoft SQL Server数据库中名称不一样；PostgreSQL数据库中的SUBSTRING()函数格式为
substring(string [from int] [for int])抽取字符串。
 - MySQL <substring query="MID((%s),%d,%d)"/>
 - PostgreSQL  <substring query="SUBSTRING((%s)::text FROM %d FOR %d)"/>
 - Microsoft SQL Server  <substring query="SUBSTRING((%s),%d,%d)"/>
 - Oracle  <substring query="SUBSTRC((%s),%d,%d)"/>

#### 连接字符串功能。ysql数据库中的CONCAT()函数。
 - MySQL  <concatenate query="CONCAT(%s,%s)"/>
 - PostgreSQL  <concatenate query="%s||%s"/>
 - rosoft SQL Server <concatenate query="%s+%s"/>
 - Oracle  <concatenate query="%s||%s"/>

#### 当用户在shell中输入一个参数（例如：banners），sqmlap会在xml查找<banner>节点，query值为“VERSION()”
```sh
    def banner(self, data):
        bannerElem = self.__doc.createElement(BANNER_ELEM_NAME)
        bannerElem.appendChild(self._createTextNode(data))
        self._addToRoot(bannerElem)
```

