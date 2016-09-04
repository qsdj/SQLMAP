# sqlmap源码阅读笔记
 - 开学就要开门红，好吧，开学要把以前的尾巴扫清，刚刚开学正好处于小学期，空闲时间多多的，趁机将以前读sqlmap的笔记整理一下，因
 自己的服务安装了蜜罐玩，没有了博客，就上传github了
                                                   
                                                  
                  .:7J5OMijui.                    
                .5UXM@B@Br72B@qY,                 
               :Fr:778u@@u@YiMPSXr                
              i7rri:urL@8qU@O1Pirv7               
              .uuYiLPuNNGFB8qZBrrvSk              
              ,MOr75BSX12OYZF7FuuFP@q             
              7BPLNB;uUFSXU251YYNBO@r             
              7@MOBL 1MOO8@@0SZSP@B@              
               @@BM   M@S UBB ;S@B@B              
               @B@B.LkNYk  :B@r7B@Mr              
              .:r.@ ,u;     .i. Ei.               
                 .:.      .    ..,                
                  .Y          .E.                 
                   .,:       :7                   
                     iq7:  7@q                    
                    .:.:vPMEii.                   
                  ..J        ZM ,                 
              , ,,  ,i.      E;. :.::             
            5B@:    Y. :   . .ZF  LB@B7           
           @B@7   7 M. r: ,. OB. 7@@M@@B          
          8B@@@L  @OYE  .:. UB@ 7@BOMOOM@:        
        i@@@@@B@0u@@0@:  :  B@BB@@OO0N5ZB@8       
       7@B@ .uE8@B@B@B@  . .@Z@M810N0P88MO@j      
       @BBB.   XB7Y@B@B7 . 0BNB8jiMMOMqqZ0BM      
       BMO@L   X@:21@@@7 . B@M@O1r@88MXN08@B      
      i@MMMM   EB:B@BEku   @@@BBUuBOGB5qqBM@      
      SBMOMB   @Z:ZN@B@7  rB@BMqr2BGMESuMOMBi     

## SQLMap 流程
- sqlmap version：0.9

## sqlmap.py 64行
```sh
 paths.SQLMAP_ROOT_PATH = modulePath()
    setPaths()
    banner()
```
- setPaths()函数在sqlmap\lib\core\commmon.py中第840行可以看出setPaths()函数是定义sqlmap的路径和文件信息如：xml地址
 ```sh
 paths.SQLMAP_XML_PATH = os.path.join(paths.SQLMAP_ROOT_PATH, "xml")
 ```
- banner()函数的作用是sqlmap在执行测试时输出sqlmap的banner和版本信息。

## sqlmap.py 73行
```sh
    try:
        init(cmdLineOptions)
        if conf.profile:
            profile()
        elif conf.smokeTest:
            smokeTest()
        elif conf.liveTest:
            liveTest()
```
 - init(cmdLineOptions)()函数是用来设置注入测试时参数的。
 
 ## sqlmap.py 81行
```sh
    else:
    	start()
```
 - start()函数，sqlmap注入测试的开始。
 - start()函数在sqlmap\lib\controller\controller.py中，controller.py 140行。
 - ```sh
   if conf.direct:
        initTargetEnv()
        setupTargetEnv()
        action()
        return True
    ```
 - conf.direct这是sqlmap -d，sqlmap进入action()函数并连接数据库。
			 - action.py中158行
			 - ```sh
			       if conf.direct:
        				conf.dbmsConnector.close()
                          ```
 - controller.py第173行进入循环参数检测。会检测是否以前测试过testSqlInj，以及初始化基本参数url/psot:get/cookie/configfire/data
```sh
for targetUrl, targetMethod, targetData, targetCookie in kb.targetUrls:
        try:
            conf.url    = targetUrl
            conf.method = targetMethod
            conf.data   = targetData
            conf.cookie = targetCookie

            initTargetEnv()
            parseTargetUrl()

            testSqlInj = False
        ```

 
## controller.py 第267行 第364行
 - setupTargetEnv()函数将解析参数、生成cookie信息、创建并输出结果。
 - checkDynParam()函数在sqlmap\lib\contoller\check.py 第569行 checkDynParam()函数检测url参数是否为动态。

```sh
elif not checkDynParam(place, parameter, value):
def checkDynParam(place, parameter, value)
``` 
## controller.py 第375行
```sh
check = heuristicCheckSqlInjection(place, parameter)
```
 - heuristicCheckSqlInjection()函数在sqlmap\lib\contoller\check.py 第496行。
```sh
   def heuristicCheckSqlInjection(place, parameter):   
 ```
   生成payloads，并进行初步的sql注入测试，并解析结果（wasLastRequestDBMSError()查看是否包含数据库的报错）。
    ```sh
    payload = "%s%s%s" % (prefix, randomStr(length=10, alphabet=['"', '\'', ')', '(']), suffix)
    payload = agent.payload(place, parameter, newValue=payload)
     Request.queryPage(payload, place, content=True, raise404=False)

    result = wasLastRequestDBMSError()
    ```
 - 同时输出根据payloads进行简单测试的结果。
   ```sh
       if result:
        infoMsg += "be injectable (possible DBMS: %s)" % (Format.getErrorParsedDBMSes() or UNKNOWN_DBMS_VERSION)
        logger.info(infoMsg)
    else:
        infoMsg += "not be injectable"
        logger.warn(infoMsg)

    return result
    ```
## controller.py 第384行
```sh
 injection = checkSqlInjection(place, parameter, value)
```
 - checkSqlInjection()函数在 sqlmap\lib\contoller\check.py 第64行，函数是进行注入类型判断，生成payloads；根据用户指定或者是
 页面报错信息来判断DBMS类型。这应该是sqlmap中最重要的一块。
 - 可以看到4中注入方式 1.盲注 2.基于错误注入 3.时间注入 4.联合查询注入
    ```sh
   if method == PAYLOAD.METHOD.COMPARISON:
   elif method == PAYLOAD.METHOD.GREP:
   elif method == PAYLOAD.METHOD.TIME:
   elif method == PAYLOAD.METHOD.UNION:
   ```
## sqlmap.py最后 shutting down at time.strftime("%X")
0x00
第一读后关于sqlmap的一般流程
 - 判断DBMS类型（自动/手工）
 - 生成payloads测试
 - METHOD.COMPARISON: METHOD.TIME：METHOD.GREP:METHOD.UNION:
 - 输出 存储 结果
0x01 
第一遍阅读（pycharm调试），笔记写的如流水帐一般。sqlmap的sql注射技巧真的一流。sqlmap值得继续去读，这次从sql注射来读，下一次要
当成工程去深入的读。

0x02
网上 曾是土木人“http://www.cnblogs.com/hongfei/category/372087.html” 的关于sqlmap源码的文章以及3xpl0it 
“https://www.91ri.org/13785.html?utm_source=tuicool&utm_medium=referral”都写的非常好。

   

 
 
