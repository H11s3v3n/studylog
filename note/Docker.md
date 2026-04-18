# Docker

### docker

***安装***：

~~~bash
bash <(wget -qO- https://xuanyuan.cloud/docker.sh)
~~~

***验证***：

~~~bash
docker --version  # 查看 Docker 版本
docker compose version  # 查看 Docker Compose 版本
~~~

***命令大全***：

**查看类**

~~~bash
docker ps           # 查看正在运行的容器
docker ps -a        # 查看所有容器（包括停止的）
docker images       # 查看本地镜像
docker info         # 查看 docker 状态
~~~

**运行容器类**

~~~bash
docker run -d \
  -p 8080:80 \
  --name mynginx \
  -v /home/kali/html:/usr/share/nginx/html \
  --restart=always \
  nginx


-d #后台运行；不加 -d：容器会占用当前终端，Ctrl+C 就停了
-p #端口映射，ip:主机端口；相当于运行容器端口
--name #给容器起名字
-v #目录挂载 本地目录:容器内目录
--restart=always #开机 / 崩溃自动重启
--network=host #直接使用宿主机网络
-it #-i：交互式，保持标准输入打开 -t：分配一个伪终端；一般用来 进入容器
~~~

***进入容器***

~~~bash	
docker exec -it 容器ID/名字 /bin/bash

-it    = 交互 + 终端（必须）
-u     = 指定用户（root）
-w     = 指定工作目录
/bin/bash = 进入后打开的终端
~~~

***启停删除***

~~~bash 
docker start 容器ID    # 启动
docker stop 容器ID     # 停止
docker restart 容器ID  # 重启
docker rm 容器ID       # 删除容器
docker rmi 镜像ID      # 删除镜像
~~~

***日志拷贝***

~~~bash
docker logs 容器ID     # 查看日志
docker cp 本地文件 容器ID:/路径  # 拷贝文件进容器
~~~

***清理***

~~~bash
docker system prune -a  # 清理所有无用镜像/容器
~~~

___



### docker-compose

***完整常用命令合集***

~~~bash
# 启动服务（后台运行）
docker-compose up -d

# 停止并删除容器、网络
docker-compose down

# 只停止，不删除
docker-compose stop

# 重启服务
docker-compose restart

# 查看运行状态
docker-compose ps

# 实时查看日志; -f 实时滚动刷新，用于排查错误
docker-compose logs -f

# 进入容器内部
docker-compose exec 服务名 bash

# 构建镜像并重启服务
docker-compose up -d --build

# 进入单独启动某个服务
docker-compose run --rm 服务名 bash
~~~



 sudo systemctl daemon-reloadsudo systemctl restart dockersudo systemctl daemon-reexec
