### 安装Docker

#### Linux

对于各大Linux发行版（Ubuntu、CentOS等），推荐使用官方脚本进行安装，方便快捷：

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh --mirror Aliyun
```

然后推荐将 `docker` 的权限移交给非 root 用户，这样使用 `docker` 就不需要每次都 `sudo` 了：

```sh
sudo usermod -aG docker $USER
```

注销用户或者重启之后就会生效。然后通过 `systemd` 服务配置 Docker 开机启动：

```bash
sudo systemctl enable docker
```

#### 配置镜像仓库

默认的镜像仓库 Docker Hub 在国外，国内拉取速度比较感人。建议参考[这篇文章](https://yeasy.gitbooks.io/docker_practice/content/install/mirror.html)配置镜像加速。

---

### 小试牛刀

#### 实验一：Hello World

运行一下来自 Docker 的 Hello World，命令如下：  

```bash
docker run hello-world
```

输出如下：	

```bash
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete 
Digest: sha256:49a1c8800c94df04e9658809b006fd8a686cab8028d33cfba2cc049724254202
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
...
```

总结一下Docker 做的事情：

1. 检查本地是否有指定的 `hello-world:latest` 镜像，如果没有，执行第 2 步，否则直接执行第 3 步
2. 本地没有指定镜像（ Unable to find xxx locally ），从 [Docker Hub](https://hub.docker.com/) 下载到本地
3. 根据本地的 `hello-world:latest` 镜像创建一个新的容器并运行其中的程序
4. 运行完毕后，容器退出，控制权返回给用户



#### 实验二：运行一个 Nginx 服务器

运行一个 [Nginx 服务器](https://baike.baidu.com/item/nginx)。运行以下命令：

```bash
docker run -p 8080:80 nginx
```

运行之后，你会发现一直卡住，也没有任何输出，但放心你的电脑并没有死机。让我们打开浏览器访问:

`ip:8080`这个时候就可以看到容器的输出了

总结一下Docker 做的事情：

1. 检查本地是否有指定的 `nginx:latest` 镜像，如果没有，执行第 2 步，否则直接执行第 3 步
2. 本地没有指定镜像（ Unable to find xxx locally ），从 [Docker Hub](https://hub.docker.com/) 下载到本地
3. 根据本地的 `nginx:latest` 镜像创建一个新的容器，**并通过 -p（--publish）参数建立本机的 8080 端口与容器的 80 端口之间的映射**，然后运行其中的程序
4. Nginx服务器程序保持运行，容器也不会退出

#### 实验三：后台运行Nginx

像 Nginx 服务器这样的进程我们更希望把它抛到后台一直运行。按 `Ctrl + C` 退出当前的容器，然后再次运行以下命令：

```bash
docker run -p 8080:80 --name my-nginx -d nginx
```

与之前不同的是，我们加了两个参数：

- --name，用于指定容器名称为my-nginx
- -d(--detach)，表示后台运行

我们还需要管理这个服务器，使用`docker ps`命令，输出如下：

```bash
 ⚡ root@VM-0-9-centos  ~  docker run -p 8080:80 --name my-nginx -d nginx
d231bfd654a3fb5621021c484b06d4b003d40d086617da16118fb197c59bf575
 ⚡ root@VM-0-9-centos  ~  docker ps                                     
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
d231bfd654a3        nginx               "/docker-entrypoint.…"   3 seconds ago       Up 2 seconds        0.0.0.0:8080->80/tcp   my-nginx
```

其相关信息为：

- 容器ID（CONTAINER ID）
- 所用镜像（Image ）为nginx
- 运行命令 /程序（ Command ）
- 创建时间（ Created ）
- 当前状态（ Status ） 已运行时间
- 端口（ Ports ）为 `0.0.0.0:8080->80/tcp`，意思是访问本机的 `0.0.0.0:8080` 的所有请求会被转发到该容器的 TCP 80 端口
- 名称（ Names ）为刚才指定的 `my-nginx`

我们还需要让容器停下来，通过 `docker stop` 命令指定容器名称或 ID 进行操作即可，命令如下：

```bash
docker stop my-nginx
docker stop d231bfd654a3
```

---

### 实战：整合springboot

#### 整合基础环境

##### mysql

##### redis

#bind 127.0.0.1 //允许远程连接

修改redis 配置问题   daemonize yes 千万要注释掉

```
sudo docker run -p 6379:6379 --name redis -v /mydata/redis/data:/data \
-v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
-d redis redis-server /etc/redis/redis.conf
```

#### dockerfile+jar构建镜像并运行

##### Dockerfile

```dockerfile
FROM openjdk:8
# MAINTAINER指明作者
MAINTAINER hxxx
# ADD 复制jar文件到镜像中去并重命名为demo.jar
ADD tree-hole-0.0.1-SNAPSHOT.jar tree-hole-v1.jar
# 容器暴露的端口是多少，就是jar在容器中以多少端口运行
EXPOSE 10083
# 容器启动之后执行的命令，java -jar demo.jar 即启动jar
ENTRYPOINT ["java","-jar","tree-hole-v1.jar"]
```

##### jar

使用maven 打包成jar包后，因为使用了springboot  内嵌了tomcat，所以可以直接使用。

先将jar包放入环境  `java -jar xxxxx.jar  `  测试是否正常。

正常的话  将jar包与Dockerfile共同放入一个文件夹中。

在当前目录下执行编译镜像

```bash
#  name:tag   镜像名：版本号
docker build -t name:tag .
# .  Dockerfile文件在当前文件夹下
```

运行： `docker run --name tree-hole -p 10083:10083 -d tree-hole:1.0`

查看运行状态： `docker ps`



---

### 常用命令总结

- docker search 关键字

- docker pull 镜像名：tag    拉取镜像

- docker images 镜像列表

- docker rmcontainer-id  删除指定镜像

- docker ps  查看运行中的容器   -a 查看所有容器

- docker start/stop   container-id   开始/停止 指定容器id

- docker run --name 容器名 -d -p 3306:3306 mysql               -d(后台运行)  ;  -p  xx :xx   端口映射  虚拟机端口：docker容器端口

- journalctl -xeu docker     查看docker 服务异常日志

  



