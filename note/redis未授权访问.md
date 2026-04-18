# Redis未授权访问

Redis 是完全开源免费的，一个灵活的高性能 key-value 数据结构存储，可以用来作为数据库、缓存

和消息队列。



Redis 主要有两个应用场景：

1. 存储缓存用的数据；
2. 需要高速读/写的场合使用它快速读/写；

| 服务                                                         | 默认端口   |
| ------------------------------------------------------------ | ---------- |
| Redis                                                        | 6379       |
| MongoDB                                                      | 27017      |
| Memcached                                                    | 11211      |
| Jboss                                                        | 8080       |
| sudo systemctl daemon-reloadsudo systemctl restart dockersudo systemctl daemon-reexec | 5900、5901 |
| Docker                                                       | 2375       |



***nmap端口扫描***

~~~bash
nmap端口扫描
nmap -v -Pn -p [port] -sV [IP]
-v：显示详细的扫描过程，包括扫描进度和统计信息。
-Pn：跳过主机发现阶段，直接进行端口扫描，假设目标主机是可达的。
-p [port]：指定要扫描的端口，可以是一个或多个端口号，例如-p 6379。
-sV：启用服务版本探测，尝试确定运行在开放端口上的应用程序及其版本号。
[IP]：目标IP地址。
~~~

___



***redis常用命令***

~~~bash
# 连接到远程Redis服务器：
redis-cli -h [hostname] -p [port] -a [password]
[hostname]：Redis服务器的地址。
[port]：Redis服务监听的端口，默认为6379。
[password]：Redis服务器的密码。
~~~

***redis基本数据操作***

~~~bash
set testkey "Hello World" # 设置键testkey的值为字符串"Hello World"
get testkey # 获取键testkey存储的字符串值
set score 99 # 设置键score的值为99
incr score # 使用INCR命令将score的值增加1
get score # 获取键score的内容
keys * # 列出当前数据库中所有的键
~~~

***redis基本配置操作***

~~~bash
config set dir /home/test # 设置工作目录
config set dbfilename redis.rdb # 设置备份文件名
config get dir # 检查工作目录是否设置成功
config get dbfilename # 检查备份文件名是否设置成功
~~~

***redis数据库管理***

~~~bash
save # 进行一次备份操作，保存数据库到磁盘
flushall # 删除所有数据
del key # 删除键为key的数据
~~~

___

***redis未授权漏洞访问原理***：

**配置不当**：redis实例直接暴露在公网可访问，监听所有网络接口

**密码保护缺失**：未设置密码、使用默认密码



***危害***：信息泄露、数据库被恶意更改、数据丢失、植入后门getshell、权限提升、远程代码执行

___

***利用方法***

1. 通过redis数据备份功能结合WEB服务，往WEB网站根目录写入一句话木马，从而得到WEB网站权限
2. 通过redis数据备份功能写定时任务，通过定时任务反弹Shell
3. 通过redis数据备份功能写SSH公钥，实现免密登录linux服务器



条件

1. 知道网站根目录绝对路径
2. 对目标网站根目录有写入权限

___

### 主从复制rce

如果把数据存储在单个Redis的实例中，当读写数据量比较大的时候，服务端就很难承受。为了应对这

种情况，Redis就提供了主从模式，主从模式就是指使用一个redis实例作为主机，其他实例都作为备份

机，其中主机和从机数据相同，而从机只负责读，主机只负责写，通过读写分离可以大幅度减轻流量

的压力，算是一种通过牺牲空间来换取效率的缓解方式。



Redis的主从复制功能允许从服务器同步主服务器的数据。然而，在Redis 4.x及以后的版本中，引入的

模块功能可能被滥用来实现远程代码执行（RCE）：

1. 模块功能滥用：攻击者通过上传恶意的Redis模块（.so文件），并利用Redis的MODULE LOAD命令加

载该模块。

2. 远程代码执行：加载的恶意模块可能包含用C语言编写的代码，该代码在Redis服务器上执行，允许攻

击者执行任意命令。



***核心 原理***：Redis 主从同步时，从节点会**全量加载主节点的 RDB 数据文件**。攻击者构造一个**伪 RDB 文件**（内含恶意 `.so`），诱骗目标加载，最终通过 `MODULE LOAD` 执行代码。

~~~bash
攻击者                                 目标Redis (从节点)
  │                                          │
  │ 1. 编译恶意 module.so                    │
  │                                          │
  │ 2. 启动伪主节点 (监听6379)               │
  │ <────────── SLAVEOF 攻击者IP 6379 ─────── │ 3. 执行SLAVEOF
  │                                          │
  │ <────────── PSYNC 全量同步请求 ─────────── │ 4. 发起同步
  │                                          │
  │ ─────── 发送「伪RDB」(实为module.so) ───> │ 5. 接收、保存、加载
  │                                          │
  │ <────────── MODULE LOAD 成功 ─────────── │ 6. 注册system.exec
  │                                          │
  │ ─────── system.exec "反弹shell" ───────> │ 7. 执行系统命令
  │                                          │
  │ ◄────────── 反弹 Shell 连接 ──────────── │ 8. 获得服务器权限

~~~

