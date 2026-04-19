# JBoss漏洞

JBoss是一个高度可扩展的、基于J2EE的开放源代码的应用服务器

JBoss 是一个管理 EJB 的容器和服务器，但 JBoss 核心服务不包括支持 servlet/JSP 的WEB容器，

一般与Tomcat或Jetty绑定使用。Jboss是 Java EE 应用服务器（就像Apache是web服务器一样），

专门用来运行Java EE程序的。



### Jboss历史漏洞

| 漏洞类型               | 漏洞名称                                              |
| ---------------------- | ----------------------------------------------------- |
| 访问控制不严导致的漏洞 | JMX Console 未授权访问 Getshell                       |
| 访问控制不严导致的漏洞 | Administration Console 弱口令 Getshell                |
| 访问控制不严导致的漏洞 | CVE-2007-1036 -- JMX Console HtmlAdaptor Getshell     |
| 访问控制不严导致的漏洞 | CVE-2010-0738 -- JMX 控制台安全验证绕过漏洞           |
| 反序列化漏洞           | CVE-2013-4810 -- JBoss EJBInvokerServlet 反序列化漏洞 |
| 反序列化漏洞           | CVE-2015-7501 -- JBoss JMXInvokerServlet 反序列化漏洞 |
| 反序列化漏洞           | CVE-2017-7504 -- JBoss 4.x JBossMQ JMS 反序列化漏洞   |
| 反序列化漏洞           | CVE-2017-12149 -- JBoss AS 6.X 反序列化漏洞           |

***Jboss历史漏洞发现***

资产信息收集

~~~bash
title="JBoss" || header="jboss" && port="8080" && country!="CN"
~~~

***Jboss漏洞检测***

~~~~bash
git clone https://gitee.com/yijingsec/jbossScan.git
cd jbossScan
python jbossScan.py -u
~~~~



___

### Jboss历史漏洞利用

#### JMX Console 未授权访问漏洞

A. 漏洞简介

JBoss的webUI界面 http://ip:port/jmx-console 未授权访问(或默认密码 admin/admin)，可导致

JBoss 的部署管理的信息泄露，攻击者也可以直接远程部署war包上传木马获取 webshell

____

#### Jboss弱口令Getshell

A. 漏洞简介

JBoss Administration Console存在默认账号密码，如果Administration Console可以登录，就可

以在后台部署war包getshell

jboss常见弱口令

~~~bash
admin/admin
jboss/admin
admin/jboss
admin/123456
admin/password
~~~

____

#### CVE-2007-1036&CVE-2010-0738

JMX Console HtmlAdaptor Getshell



A. 漏洞简介

JBoss `/jmx-console/HtmlAdaptor` 未授权访问，攻击者可直接调用后台 `jboss.admin:service=DeploymentFileRepository` 里的 `store()` 方法上传任意文件，直接 Getshell。

***CVE-2010-0738***

利用原理与CVE-2007-1036相同，只不过利用HEAD请求方法绕过GET和POST请求的限制



___

#### CVE-2015-7501

JMXInvokerServlet 反序列化漏洞

A. 漏洞简介

JBoss 的 `/invoker/JMXInvokerServlet` 接口会读取并处理客户端传入的 Java 对象，在 HttpInvoker 组件的 `ReadOnlyAccessFilter` 过滤器中，未对用户输入数据流做任何安全校验便直接执行反序列化操作。攻击者可借助 Apache Commons Collections 等内置 Gadget 构造恶意序列化数据，远程触发漏洞并实现**任意代码执行**，该漏洞属于典型的 **Java 反序列化漏洞**。



B.漏洞利用

浏览器访问 http://ip:port/invoker/JMXInvokerServlet，直接下载文件，说明存在接口，及存在漏洞

通过 Java 工具结合 CommonsCollections 利用链，**编译 → 生成 payload → 发送至 JBoss 接口**，即可触发反序列化漏洞实现远程命令执行。

___

#### CVE-2017-7504

JBossMQ JMS 反序列化漏洞

A. 漏洞简介

JBoss AS 4.x 及更早版本中，JbossMQ 的 JMS over HTTP Invocation Layer 里的 **HTTPServerILServlet.java** 存在反序列化漏洞，远程攻击者可构造恶意序列化数据，利用该漏洞实现任意代码执行。

该漏洞编号为 **CVE-2017-7504**，其原理与 **CVE-2015-7501** 类似，仅利用路径不同，漏洞入口为：**/jbossmq-httpil/HTTPServerILServlet**。



B.漏洞利用

访问http://IP地址:端口/jbossmq-httpil/HTTPServerILServlet，若出现如下界面则存在漏洞

通过 Java 工具结合 CommonsCollections 利用链，**编译 → 生成 payload → 发送至 JBoss 接口**，即可触发反序列化漏洞实现远程命令执行。

____

#### CVE-2017-12149

Jboss Application Server反序列化命令执行漏洞

A. 漏洞简介

该漏洞属于 **Java 反序列化漏洞**，其成因是 Jboss 的 HttpInvoker 组件中，`ReadOnlyAccessFilter` 过滤器未对客户端传入的数据流进行任何安全校验，便直接尝试执行反序列化操作，进而被攻击者利用。



B. 漏洞发现

访问http://IP地址:端口/invoker/readonly，若返回如下显示状态码为500的报错界面,则证明漏洞存在



____

| CVE 编号           | 核心成因                                                     | 访问路径                                    | 影响版本            |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------- | ------------------- |
| **CVE-2015-7501**  | 未校验直接反序列化，依赖 **Commons Collections Gadget** 链NVD | `/invoker/JMXInvokerServlet`                | JBoss AS 4.x 及更早 |
| **CVE-2017-7504**  | 未校验直接反序列化，逻辑与上一致                             | `/jbossmq-httpil/HTTPServerILServlet`       | JBoss AS 4.x 及更早 |
| **CVE-2017-12149** | 未校验直接反序列化，依赖 **Commons Collections Gadget** 链   | `/invoker/readonly`（ReadOnlyAccessFilter） | JBoss AS 5.x/6.x    |

- **路径不同**：CVE-2015-7501 走 JMX 接口，CVE-2017-7504 走 JBossMQ 的 JMS HTTP 层，CVE-2017-12149 走 HttpInvoker 的只读过滤器。
- **版本覆盖**：前两者针对 4.x 及更早，后者针对 5.x/6.x。
- **利用链共性**：均依赖 **Commons Collections**（尤其是 `InvokerTransformer`）构造恶意 payload 触发 RCE。

### 一句话总结

**本质都是 “未校验→直接反序列化→执行任意代码”**，只是不同版本的 JBoss 在不同组件 / 路径上暴露了这个问题，利用链核心一致。