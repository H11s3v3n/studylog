# V2Ray搭建详细图文教程 #



## 前言

此教程仅本人学习记录，只能保证自己看得懂，写得粗糙详细教程前往[V2Ray教程](https://github.com/233boy/v2ray)

## V2Ray

官网：[https://www.v2fly.org/](https://www.v2fly.org/)

由于 V2Ray 的配置相对来说是很繁琐,直接使用大佬的[V2Ray一键安装脚本](https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC)，使用 V2Ray 便会显得轻松多了。

## 流程

- 想要搭建 V2Ray，就必须要拥有一台 VPS。
- 我们必须要知道 VPS、IP 地址，root 用户密码，SSH 端口
- 使用 Xshell 配置 VPS SSH 信息，然后登录
- 使用脚本安装 V2Ray
- 此时你可以使用客户端配置 V2Ray 使用了

## 安装V2Ray

输入下面命令回车，你可以复制过去，然后在 Xshell 界面按 Shift + Insert 即可粘贴，不能按 Ctrl + V 的。

``sudo su``

``bash <(curl -s -L https://git.io/v2ray.sh)``

当你执行了上面的安装命令，并且没有错误提示的话，那么你就能看到类似下面的图片



linux命令，可以查看v2ray的进程是否存在

``ps -ef | grep -v grep | grep v2ray``

## 防火墙

一般的云服务器都会有防火墙来阻挡非法流量，你需要把刚才的端口加到安全组策略里
面。

``例子 telnetIPport，使用你的真实IP和端口代替``
``telnet a.b.c.d 12345``

[https://tcp.ping.pe/](https://tcp.ping.pe/)测试防火墙

## V2Ray管理面板

现在可以尝试一下输入 `v2ray` 回车，即可管理 V2Ray



提示，如果你不想执行任何功能，直接按 Enter 回车退出即可。

## 具体流程

### 服务端

执行一下代码创建一个ss配置

``v2ray add ss``

他会自动生成端口密码，到云防火墙把你那个端口号添加，用这个[https://tcp.ping.pe/](https://tcp.ping.pe/)测试防火墙网站去测试一下,保证为successful，记录配置的端口和密码

---

### 客户端

将air.yaml的这部分改为自己vps对应的

```hmtl
、# Shadowsocks
- name: "ss"
  type: ss
  server: vps-ip
  port: 端口号
  cipher: chacha20-ietf-poly1305
  password: "实际密码"
 
```

上传的Clash for Verge或者其他机场软件