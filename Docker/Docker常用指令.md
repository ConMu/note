

##### docker启动

```bash
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

##### 获取镜像

```bash
$ docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签] # $ docker pull ubuntu:18.04,上面的命令中没有给出 Docker 镜像仓库地址，因此将会从 Docker Hub （docker.io）获取镜像
```

##### 列出镜像

```bash
$ docker image ls # 列出已经下载下来的镜像
    $ docker image ls -a # 显示包括中间层镜像在内的所有镜像的话
    $ docker image ls ubuntu # 根据仓库名列出镜像
    $ docker image ls -f dangling=true #列出虚悬镜像
    $ docker image ls -q # 以特定格式显示：id
    $ docker image ls --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}" # # 以特定格式显示：有标题、行等信息【更加实用】
$ docker system df # 查看镜像、容器、数据卷所占用的空间
```

##### 删除镜像

```bash
$ docker image prune # 删除虚悬镜像
$ docker image rm [选项] <镜像1> [<镜像2> ...] # 删除本地镜像
	$ docker image rm 501 # 删除 501 id前缀的镜像
	$ docker image rm centos # <仓库名>:<标签>
	$ docker image rm $(docker image ls -q redis) # 批量删除仓库名为redis的镜像
	$ docker image rm $(docker image ls -q -f before=mongo:3.2) # 删除所有在 mongo:3.2 之前的镜像
```

##### 构建镜像

```bash
$ docker build [选项] <上下文路径/URL/->
```



##### 容器存为镜像

```bash
$ docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]] # 将容器存为镜像，数据也会存储
```

##### 运行容器

```bash
$ docker run -it --rm ubuntu:18.04 bash # -i：交互式操作，-t 终端, --rm：容器退出后随之将其删除, ubuntu:18.04：用 ubuntu:18.04 镜像为基础来启动容器, bash：放在镜像名后的是 命令
	$ docker run --name webserver -d -p 80:80 nginx # 用 nginx 镜像启动一个容器，命名为 webserver，并且映射了 80 端口
```

##### 查看容器

```bash
$ docker ps -a # 查看所有容器
```

##### 删除容器

```bash
$ docker rm -f $(docker ps -aq) # 删除对应容器
```

##### 进入容器

```bash
$ docker exec -it webserver bash # 以交互式终端方式进入 webserver 容器，并执行了 bash 命令
```

##### 启停容器

```bash
$ docker stop Name或者ID # 停止
$ docker start Name或者ID # 启动
$ docker kill Name或者ID  # 杀死
$ docker restart name或者ID # 重启
```

##### 退出容器

```bash
$ exit
```

