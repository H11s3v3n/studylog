# 一、文件与目录基础认知

在 Linux 中：

- 一切皆文件（包括设备、网络、进程信息）
- 目录本质也是文件（存储文件名 → inode 映射）

常见类型：

- 普通文件 `-`
- 目录 `d`
- 链接 `l`
- 设备文件 `b/c`

查看：

```
ls -l
```

------

# 二、文件查看操作

## 1. 查看文件内容

```
cat file.txt        # 一次性输出（小文件）
tac file.txt        # 倒序输出
less file.txt       # 推荐（支持翻页）
more file.txt       # 老工具
head -n 10 file.txt # 前10行
tail -n 10 file.txt # 后10行
tail -f log.txt     # 实时日志（渗透/运维常用）
```

------

## 2. 查看文件信息

```
ls -lh             # -l = 详细信息（权限、大小、时间、所有者）-h （人类可读）
stat file.txt       # 详细元数据（inode、时间）
file file.txt       # 判断文件类型（很重要）
```

------

# 三、文件创建与编辑

## 1. 创建文件

```
touch a.txt
echo "hello" > a.txt      # 覆盖写
echo "world" >> a.txt     # 追加写
```

------

## 2. 编辑文件

常用编辑器：

### （1）vi / vim（必须掌握）

```
vim file.txt
```

模式：

- 普通模式
- 插入模式 `i`
- 命令模式 `:wq`

------

### （2）nano（简单）

```
nano file.txt
```

------

# 四、文件操作（核心）

## 1. 复制

```
cp file1 file2
cp -r dir1 dir2 # 递归复制目录
```

------

## 2. 移动 / 重命名

```
mv a.txt b.txt      # 重命名
```

我需要学习的Linux命令

展开收起

你现在是在往**安服工程师 / 安全方向**走，那 Linux 命令不能只是“会用”，而是要做到**能排查问题 + 能分析环境 + 能打点定位**。我给你一套**分阶段必学清单（实战导向）**，不是简单罗列，而是带用途的。

------

# 一、基础必会（入门 → 日常操作）

这一层你必须做到：**不用查资料直接敲**

## 1. 文件与目录

```
ls      # 列出文件
ll      # 常用别名（详细）
cd      # 切换目录
pwd     # 当前路径
mkdir   # 创建目录
rm      # 删除
cp      # 复制
mv      # 移动/重命名
```

👉 安全场景：

- 找 Web 目录
- 定位日志文件
- 清理痕迹（蓝队/红队都用）

------

## 2. 文件查看（非常重要）

```
cat
less
head
tail -f
```

👉 核心能力：

- 看日志
- 找漏洞触发点
- 分析攻击行为

------

## 3. 查找文件

```
find / -name "*.php"
locate file.txt
which python
whereis nginx
```

👉 安全必用：

- 找 webshell
- 找敏感文件（config.php / .env）

------

# 二、进阶核心（必须熟练）

这一层是**安服工程师分水岭**

## 1. 文本处理（三剑客）

```
grep "password" file.txt #在文件里找包含关键词的行
grep -r "eval" .         #在当前目录及所有子目录里,搜索所有文件,找包含 eval 的行
awk '{print $1}'         #按列提取文字,$1，$NF最后一段
sed 's/old/new/g'        #作用：批量替换文字

    s = substitute 替换
    old = 要被替换的内容
    new = 替换成什么
    g = global 全局替换

```

👉 实战用途：

- 扫日志找攻击
- 批量分析数据
- 提取 IP / URL

------

## 2. 权限管理（安全核心）

```
chmod 777 file
chmod +x script.sh
chown www-data:www-data file
id
whoami
```

👉 安全重点：

- 提权
- 权限误配（777 是漏洞）
- webshell 权限问题

------

## 3. 压缩与解压

```
tar -cvf a.tar dir/
tar -xvf a.tar
tar -czvf a.tar.gz dir/
tar -xzvf a.tar.gz
zip / unzip
```

👉 用途：

- 传输数据
- 打包日志
- 工具部署

------

# 三、网络相关（安全必学）

## 1. 网络状态

```
ifconfig
ip a
netstat -tunlp
ss -tunlp
```

👉 能力：

- 看端口
- 找后门
- 判断服务

------

## 2. 网络测试

```
ping
curl http://target
wget http://xxx
```

👉 渗透必用：

- 测试接口
- 打请求
- 下载 payload

------

# 四、进程管理（排查必备）

```
ps aux
top
htop
kill -9 PID
```

👉 安全用途：

- 查木马进程
- 杀后门
- 定位异常CPU

------

# 五、系统信息（环境侦察）

```
uname -a
cat /etc/passwd
cat /etc/shadow
history
```

👉 安全用途：

- 信息收集
- 提权准备
- 找密码痕迹

------

# 六、权限提升 & 安全重点命令（重点！）

```
sudo -l
find / -perm -4000 2>/dev/null   # SUID
crontab -l
```

👉 这是面试必问 + 实战核心：

- 提权路径
- 定时任务后门
- SUID 利用

------

# 七、日志分析（安服核心能力）

```
tail -f /var/log/auth.log
grep "Failed password" auth.log
grep "POST" access.log
```

👉 你以后干的就是这个：

- 找爆破
- 找扫描
- 找攻击路径

------

# 八、必须掌握的组合技（高手标志）

```
ps aux | grep nginx
cat access.log | grep "sqlmap"
netstat -tunlp | grep 80
```





# find高级用法



~~~bash
#!/bin/bash

# ==================== find 高级用法合集 ====================

# 1. 基本：按文件名查找
find /var/www -name "index.php"

# 2. 不区分大小写找文件
find / -iname "webshell*.php"

# 3. 只找普通文件（排除目录）
find / -type f -name "*.php"

# 4. 只找目录
find / -type d -name "backup"

# 5. 按大小查找：大于 1MB
find / -size +1M

# 6. 按大小查找：小于 100KB
find / -size -100k

# 7. 最近 1 天内修改过的文件（排查后门常用）
find / -mtime -1

# 8. 最近 10 分钟内修改
find / -mmin -10

# 9. 按权限查找：权限为 777 的文件
find / -perm 777

# 10. 多个条件组合：php文件 + 1天内修改
find /var/www -type f -name "*.php" -mtime -1

# 11. 找到后直接执行命令：查看文件内容
find / -name "config.php" -exec cat {} \;

# 12. 找到后删除（慎用）
find /tmp -name "*.tmp" -exec rm -f {} \;

~~~





#  grep、awk、sed用法

~~~bash
#!/bin/bash

# ==================== grep 用法 ====================

# 1. 在单个文件里搜索关键词 password
grep "password" file.txt

# 2. 递归搜索当前目录所有文件，找含 eval 的行（查后门常用）
grep -r "eval" .

# 3. 忽略大小写搜索
grep -i "root" file.txt

# 4. 只显示匹配到的内容
grep -o "pass.*" file.txt

# 5. 显示行号
grep -n "db_host" config.php


# ==================== awk 用法 ====================

# 1. 打印第1列
awk '{print $1}' file.txt

# 2. 打印第1列和第3列
awk '{print $1,$3}' file.txt

# 3. 打印整行
awk '{print $0}' file.txt

# 4. 打印最后一列
awk '{print $NF}' file.txt

# 5. 过滤第3列等于 200 的行（常用在日志）
awk '$3 == 200 {print}' access.log


# ==================== sed 用法 ====================

# 1. 替换所有 old 为 new（不修改原文件）
sed 's/old/new/g' file.txt

# 2. 直接修改原文件（危险！慎用）
sed -i 's/old/new/g' file.txt

# 3. 删除包含 test 的行
sed '/test/d' file.txt

# 4. 替换第1行内容
sed '1c hello' file.txt

~~~

# tar用法

bash



运行









```bash
#!/bin/bash

# ==================== tar 压缩解压 ====================

# 1. 打包：把 dir 目录打包成 a.tar（只打包，不压缩）
tar -cvf a.tar dir/

# 2. 解压：把 a.tar 解压出来
tar -xvf a.tar

# 3. 压缩打包：把 dir 打包 + 压缩成 a.tar.gz（最常用）
tar -czvf a.tar.gz dir/

# 4. 解压：把 .tar.gz 压缩包解压（最常用）
tar -xzvf a.tar.gz


# ==================== zip / unzip ====================

# 5. zip 压缩：压缩文件/目录
zip -r a.zip dir/

# 6. unzip 解压：解压 zip 包
unzip a.zip
```

------

# 逐行超直白解释（秒懂）

## 一、tar 参数先记住

- **c** = create（创建压缩包）
- **x** = extract（解压）
- **v** = verbose（显示过程）
- **f** = file（指定文件名）
- **z** = gzip（压缩成 .gz 格式）



~~~bash
#!/bin/bash

# ==================== tar 压缩解压 ====================

# 1. 打包：把 dir 目录打包成 a.tar（只打包，不压缩）
tar -cvf a.tar dir/

# 2. 解压：把 a.tar 解压出来
tar -xvf a.tar

# 3. 压缩打包：把 dir 打包 + 压缩成 a.tar.gz（最常用）
tar -czvf a.tar.gz dir/

# 4. 解压：把 .tar.gz 压缩包解压（最常用）
tar -xzvf a.tar.gz


# ==================== zip / unzip ====================

# 5. zip 压缩：压缩文件/目录
zip -r a.zip dir/

# 6. unzip 解压：解压 zip 包
unzip a.zip

~~~

