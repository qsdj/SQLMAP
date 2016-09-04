# xml�ļ���
����banner�ļ���\errors.xml\livetests.xml\payloads.xml\phpids_rules.xml\queries.xml

## banner�ļ���
 - ��banner�ļ����У���cookie��generic�����������ͣ���mssql/mysql��oracle��postgresql��servlet��java��д�ķ������˳���
��sharepoint��Microsoft Share Point���Լ�httpͷ�е�x-aspnet-version��x-powered-by��

## payloads.xml
 - payloads.xml��sqlmap�ڽ���sqlע�����ʱʹ�õ�payloads���磺
 - AND "[RANDSTR]"="[RANDSTR] AND���
 - OR NOT [RANDNUM]=[RANDNUM1] OR���
 
## phpids_rules php���ϵͳ������
 

#### queries.xml
<root>Ϊxml�ļ��ĸ��ڵ�,<root>���ڵ����11��<dbms>�ڵ㣬��<dbms>
�ڵ��value���Կ�����ÿһ��<dbms>�ڵ����һ��sqlmap֧�ֵ����ݿ⡣MySQL��PostgreSQL��
Microsoft SQL Server��Oracle��SQLite��Microsoft Access��Firebird��SAP MaxDB��Sybase��
IBM DB2��HSQLDB������ÿһ��Ԫ�ؽڵ��ж���һ��query����ֵ�����Կ���sqlmap�Ǹ����û���
shell�������������ǲ�����xml�ļ���ѡ����Ӧ��query����ֵ��

����ѡ�񳣼���MySQL��Microsoft SQL Server��Oracle��PostgreSQL5�����ݿ⣬���Ƚ�
�������Ե�query����ֵ��

#### cast()������ת�����ͺ�������Դֵת��ΪĿ�����ͣ�AS�ؼ��ֽ�Դֵ%S��Ŀ������CHAR�ָ���
���Կ���4�б��ʽ���ַ������ǲ�һ���ģ���PostgreSQL�����ĵ��п��Կ�����char(n) �� varchar(n) 
�ĸ���ֱ��� character(n) �� character varying(n) �ı����� û�г��������ʵ�character ���� character(1)��
 - MYSQL <cast query="CAST(%s AS CHAR)"/>
 - PostgreSQL <cast query="CAST(%s AS CHARACTER(10000))"/>
 - Microsoft SQL Server <cast query="CAST(%s AS NVARCHAR(4000))"/>
 - Oracle<cast query="CAST(%s AS VARCHAR(4000))"/>

#### ��ȡԴֵ���ַ������� CHAR_LENGTH(%S)����Դֵ���ַ����ȣ����ֽ��ַ���һ���������ַ�������LENGTH()
�����ֺ���ĸ��һ���ַ��������������ַ����ȡ�SQLserver��LTRIM()�����ǳ�ȥ�ַ���ͷ���ո�STR()�����Ƿ���һ��
���ֵĵ�Ч�ַ�����LEN()�����Ƿ����ı��ֶ���ֵ�ó��ȡ�
 - MYSQL <length query="CHAR_LENGTH(%s)"/>
 - PostgreSQL <length query="LENGTH(%s)"/>
 - Microsoft SQL Server <length query="LTRIM(STR(LEN(%s)))"/>
 - Oracle  <length query="LENGTH(%s)"/>
 
#### Դֵ��ΪNULL������Դֵ�����򷵻�NUL��
�����ص����������ֺ����ַ���ֵ��ȡ��ȥ���������Ļ�����sql server�е�ISNULL()
����Ҫ��ǰ����������һ����PostgreSQL�е�COALESCE()��������ִ�б��ʽ��ֱ��������NULLֵ��ֹͣ�����ظ�ֵ�������ΪNULL��
���ؿ�ֵ��Oracle���ݿ��е�NVL()������ʽ��NVL( string1, replace_with)�����string1ΪNULL����replace_with���������ֵ��
ΪNULL������NULLֵ��
 - MYSQL <isnull query="IFNULL(%s,' ')"/>
 - PostgreSQL <isnull query="COALESCE(%s,' ')"/>
 - Microsoft SQL Server  <isnull query="ISNULL(%s,' ')"/>
 - Oracle <isnull query="NVL(%s,' ')"/>
 
#### ���ݿ�Ķ����
 - MySQL <delimiter query=","/>
 - PostgreSQL <delimiter query="||"/>
 - Microsoft SQL Server  <delimiter query="+"/>
 - Oracle  <delimiter query="||"/>
 
#### LIMIT()����ǿ��select��䷵��ָ���ļ�¼����PostgreSQL 8.1 �����ĵ��п��Կ����������� LIMIT��OFFSET �Ӿ�֮����Ϳ��Լ���
ԭ����ѯ����ѯ�����Ľ���е�һ���������С���SELECT TOP���ڷ���ָ���ļ�¼����Oracle���ݿ��е�ROWNUM()����ͬ�������ڷ�ҳ��
 - MySQL <limit query="LIMIT %d,%d"/>
 - PostgreSQ <limit query="OFFSET %d LIMIT %d"/>
 - Microsoft SQL Server <limit query="SELECT TOP %d "/>
 - Oracle <limit query="ROWNUM AS LIMIT %s) WHERE LIMIT"/>

#### ORDER BY������ڶԽ������������
 - MySQL <order query="ORDER BY %s ASC"/>
 - PostgreSQL  <order query="ORDER BY %s ASC"/>
 - rosoft SQL Server <order query="ORDER BY %s ASC"/>
 - Oracle  <order query="ORDER BY %s ASC"/>

#### COUNT()�����Ƿ���ƥ��������������
 - MySQL��Microsoft SQL Server��Oracle��PostgreSQL <count query="COUNT(%s)"/>

#### MYSQL���ݿ��е�MID()�������ڵõ��ַ�����һ���֣�Oracle��Microsoft SQL Server���ݿ������Ʋ�һ����PostgreSQL���ݿ��е�SUBSTRING()������ʽΪ
substring(string [from int] [for int])��ȡ�ַ�����
 - MySQL <substring query="MID((%s),%d,%d)"/>
 - PostgreSQL  <substring query="SUBSTRING((%s)::text FROM %d FOR %d)"/>
 - Microsoft SQL Server  <substring query="SUBSTRING((%s),%d,%d)"/>
 - Oracle  <substring query="SUBSTRC((%s),%d,%d)"/>

#### �����ַ������ܡ�ysql���ݿ��е�CONCAT()������
 - MySQL  <concatenate query="CONCAT(%s,%s)"/>
 - PostgreSQL  <concatenate query="%s||%s"/>
 - rosoft SQL Server <concatenate query="%s+%s"/>
 - Oracle  <concatenate query="%s||%s"/>

#### ���û���shell������һ�����������磺banners����sqmlap����xml����<banner>�ڵ㣬queryֵΪ��VERSION()��
```sh
    def banner(self, data):
        bannerElem = self.__doc.createElement(BANNER_ELEM_NAME)
        bannerElem.appendChild(self._createTextNode(data))
        self._addToRoot(bannerElem)
```

