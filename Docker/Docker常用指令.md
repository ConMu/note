

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
$ docker system df # 查看镜像、容器、数据卷所占用的空间
$ docker image ls -a # 显示包括中间层镜像在内的所有镜像的话
$ docker image ls ubuntu # 根据仓库名列出镜像
$ docker image ls -f dangling=true #列出虚悬镜像
$ docker image ls -q # 以特定格式显示：id
$ docker image ls --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}" # # 以特定格式显示：有标题、行等信息【更加实用】
```

##### 删除镜像

```bash
$ docker image prune # 删除虚悬镜像

```



##### 运行容器

```bash
$ docker run -it --rm ubuntu:18.04 bash # -i：交互式操作，-t 终端, --rm：容器退出后随之将其删除, ubuntu:18.04：用 ubuntu:18.04 镜像为基础来启动容器, bash：放在镜像名后的是 命令
```



##### 退出容器

```bash
$ exit
```

