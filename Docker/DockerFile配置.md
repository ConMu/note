参考资料：https://yeasy.gitbook.io/docker_practice/image/build

##### 配置步骤

在一个空白目录中，建立一个文本文件，并命名为 `Dockerfile`：

```bash
$ mkdir mynginx
$ cd mynginx
$ touch Dockerfile
```

内容为：

```bash
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```

在 `Dockerfile` 文件所在目录执行：

```bash
$ docker build -t nginx:v3 .
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM nginx
 ---> e43d811ce2f4
Step 2 : RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
 ---> Running in 9cdc27646c7b
 ---> 44aa4490ce2c
Removing intermediate container 9cdc27646c7b
Successfully built 44aa4490ce2c
```



##### DockerFile书写

```bash
# 每个RUN创建一层镜像
# Dockerfile 支持 Shell 类的行尾添加 \ 的命令换行方式，以及行首 # 进行注释的格式
FROM debian:stretch

RUN set -x; buildDeps='gcc libc6-dev make wget' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```

##### Dockerfile指令详解E

```bash
# COPY 指令将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置
$ COPY [--chown=<user>:<group>] <源路径>... <目标路径>
$ COPY [--chown=<user>:<group>] ["<源路径1>",... "<目标路径>"]
# CMD 指令就是用于指定默认的容器主进程的启动命令的
$ CMD <命令>
# ENV 环境变量配置
$ ENV <key> <value>
$ ENV <key1>=<value1> <key2>=<value2>...
# EXPOSE 暴露端口 声明容器运行时提供服务的端口 声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射
$ EXPOSE <端口1> [<端口2>...]
# WORKDIR 指定工作目录
$ WORKDIR <工作目录路径>

```

