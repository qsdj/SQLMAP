# sqlmap使用tamper脚本有较大机会绕过WAF，获得更高的效率
## sqlmap 0.9中内置9个tamper脚本

## between.py
 - between.py 的作用是替换>(大于号)，for example：  A > B 变成 A NOT BETWEEN 0 AND B
```sh
elif payload[i] == ">" and not doublequote and not quote:
	retVal += " " if i > 0 and not payload[i-1].isspace() else ""
	retVal += "NOT BETWEEN 0 AND"
	retVal += " " if i < len(payload) - 1 and not payload[i+1].isspace() else ""
```
## charencode.py
 - d对payloads进行url编码  Example: 'SELECT FIELD FROM%20TABLE' becomes '%53%45%4c%45%43%54%20%46%49%45%4c%44%20%46%52%4f%4d%20%54%41%42%4c%45'
```sh
while i < len(payload):
	if payload[i] == '%' and (i < len(payload) - 2) and payload[i+1] in string.hexdigits and payload[i+2] in string.hexdigits:
		retVal += payload[i:i+3]
		i += 3
	else:
		retVal += '%%%X' % ord(payload[i])
		i += 1
```

## charunicodeencode.py
 - payloads unicode编码 Example: 'SELECT FIELD%20FROM TABLE' becomes '%u0053%u0045%u004c%u0045%u0043%u0054%u0020%u0046%u0049%u0045%u004c%u0044%u00
 20%u0046%u0052%u004f%u004d%u0020%u0054%u0041%u0042%u004c%u0045'
```sh
while i < len(payload):
	if payload[i] == '%' and (i < len(payload) - 2) and payload[i+1] in string.hexdigits and payload[i+2] in string.hexdigits:
		retVal += "%%u00%s" % payload[i+1:i+3]
		i += 3
	else:
		retVal += '%%u00%X' % ord(payload[i])
		i += 1
```
## ifnull2ifisnull.py
 - Example: 'IFNULL(1, 2)' becomes 'IF(ISNULL(1), 2, 1)'  ISNULL()值为NULL返回1，否则返回0.
```sh
if comma and end:
	A = payload[index + len("IFNULL("):comma]
	B = payload[comma + 1:end]
	newVal = "IF(ISNULL(%s),%s,%s)" % (A, B, A)
	payload = payload[:index] + newVal + payload[end+1:]
```
## randomcase.py
 - 随机大小写替换  Example: 'INSERT' might become 'InsERt'
```sh
if payload:
	for match in re.finditer(r"[A-Za-z_]+", retVal):
		word = match.group()

		if word.upper() in kb.keywords:
			newWord = str()

			for i in xrange(len(word)):
				newWord += word[i].upper() if randomRange(0, 1) else word[i].lower()
```

## randomcomments.py
 - 用/**/分隔 Example: 'INSERT' becomes 'IN/**/S/**/ERT'
```sh
for i in xrange(1, len(word) - 1):
	newWord += "%s%s" % ("/**/" if randomRange(0, 1) else "", word[i])
```

## space2comment.py
 - 用/**/替换空格  Example: 'SELECT id FROM users' becomes 'SELECT/**/id/**/FROM/**/users'
```sh
elif payload[i]==" " and not doublequote and not quote:
	retVal += "/**/"
```
## space2plus.py
 - 用+替换空格 Example: 'SELECT id FROM users' becomes 'SELECT+id+FROM+users'
```sh
elif payload[i]==" " and not doublequote and not quote:
	retVal += "+"
```

## space2randomblank.py
 - 从字符集中blanks = ['\r', '\n', '\t']随机选择一个替换空格   Example: 'SELECT id FROM users' becomes 'SELECT\rid\tFROM\nusers'
```sh
elif payload[i]==" " and not doublequote and not quote:
	retVal += random.choice(blanks)
```
