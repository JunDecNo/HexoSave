---

title: Docker的基本使用

date: 2023-10-08 17:13:35

author: JunDecNo

summary: 帮助了解Docker的基本使用

tags: 

 - Docker

 - 工具使用

 - 基本使用

categories: 工具使用

img: https://www.nesabamedia.com/wp-content/uploads/2021/02/Pengertian-Docker-Beserta-Fungsinya-680x350.png

---



## Docker 概述



### 安装docker

	环境准备

docker一般是运行在linux系统上的. 

如果在windows上运行, 需要使用windows自带的wsl服务



	 环境查看

[root@192 ~]# uname -r

5.14.0-325.el9.x86_64



	安装

1. 卸载旧版本docker

2. 安装, 使用仓库安装

	1. `sudo yum install -y yum-utils` 安装依赖

	2. 设置镜像的仓库 本文使用阿里云

	3. `yum install docker-ce docker-ce-cli containerd.io`

	4. 启动docker `systemctl start docker`

	5. 使用`docker version`查看是否安装成功



```shell

docker run <container-name> #运行docker环境,如果本地没有,就回去network上pull

docker images # 查看docker的环境

REPOSITORY    TAG       IMAGE ID       CREATED        SIZE

hello-world   latest    9c7a54a9a43c   2 months ago   13.3kB

```



### 阿里云镜像加速

1. 登录阿里云找到容器服务![](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202307300431335.png)

2. 找镜像加速地址![](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202307300432557.png)

3. 配置具体环境, 复制代码运行即可

```shell

sudo mkdir -p /etc/docker 

sudo tee /etc/docker/daemon.json <<-'EOF' 

{ "registry-mirrors": ["https://5qn4r3mz.mirror.aliyuncs.com"] 

}

EOF 

sudo systemctl daemon-reload 

sudo systemctl restart docker

```

这样就可以使用阿里云加速docker hub了. 

### Docker底层原理

Docker是一个CLient-Server结构的系统, Docker的守护进程运行在主机上, 通过Socker从客户端访问.  

![[Drawing 2023-07-30 04.40.47.excalidraw]]

**Docker为什么比VM快**

![](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202307300451843.png)

Docker层数更少. 

docker利用宿主机的内核, vm需要系统OS

docker依赖于宿主系统, 而vm不需要这样. 



## Docker的常用命令



### 帮助命令

```shell

docker version # 显示docker的版本信息

docker info    # 显示docker的系统信息, 包括容器等

docker 命令 --help #显示帮助命令

```

帮助文档的地址:[Dockerfile reference | Docker Documentation](https://docs.docker.com/engine/reference/builder/)



### 镜像命令

**docker images**显示所有本地的主机上的镜像

```shell

REPOSITORY    TAG       IMAGE ID       CREATED        SIZE

镜像的仓库源   标签      镜像id         创建时间        大小 

```

-a --all 显示所有镜像

-q --quiet 只显示id

**docker search**搜索docker的镜像

```shell

NAME                            DESCRIPTION                                      STARS     OFFICIAL   AUTOMATED

mysql                           MySQL is a widely used, open-source relation…   14338     [OK]       

mariadb                         MariaDB Server is a high performing open sou…   5474      [OK]       

percona                         Percona Server is a fork of the MySQL relati…   618       [OK]       

phpmyadmin                      phpMyAdmin - A web interface for MySQL and M…   840       [OK]      

bitnami/mysql                   Bitnami MySQL Docker Image                       93                   [OK]

```

--filter=<筛选条件> 类似于Github的搜索方式

**docker pull**下载远程的docker镜像

```shell

[root@192 ~]# docker pull mysql # 不写tag就下载最新

Using default tag: latest

# docker下载采用分层下载

72a69066d2fe: Pull complete 

93619dbc5b36: Pull complete 

99da31dd6142: Pull complete 

626033c43d70: Pull complete 

37d5d7efb64e: Pull complete 

ac563158d721: Pull complete 

d2ba16033dad: Pull complete 

688ba7d5c01a: Pull complete 

00e060b6d11d: Pull complete 

1c04857f594f: Pull complete 

4d7cfa90e6ea: Pull complete 

e0431212d27d: Pull complete 

Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709  #签名

Status: Downloaded newer image for mysql:latest

docker.io/library/mysql:lates= # 真实地址

```

	分层下载,如果存在相同的内容,就公用后不再下载

	指定标签的命令 `docker pull MySQL:5.0.7`

**docker rmi**删除镜像

```shell

docker rmi -f 容器的id-1 <容器的id-2> ...

docker rmi -f $(docker images -aq) # 删除所有的容器<docker images -aq>返回所有的容器id,这样就可以全部删除了. 

```

### 容器命令

说明: 有了镜像才可以创建容器, 本次使用centos测试

==容器和镜像的区别?==[[01-ToolQuestion#Docker容器和镜像的关系]]

```shell

docker pull centos

```

**新建容器并启动**

```shell

docker run [可选参数] image

# 参数说明

--name="Name" 容器名字 xxxx

-d            后台运行方式

-it           使用交互方式运行, 进入容器查看内容

-p            指定容器端口 -p 

	-p 主机端口:容器端口(常用)

	-p 容器端口

	-p IP:主机端口:容器端口

-P            随机指定端口

-rm           用完就删除

#测试启动并进入容器

docker run -it centos /bin/bash # -it表示使用交互方式, /bin/bash表示使用终端



#退出容器的命令

exit



```

**列出所有的启动的容器**

```shell

# docker ps 显示当前正在运行的容器 -a显示历史所有的容器

# -n=? 显示容器的后?个容器

# -q 显示编号

# 

[root@192 ~]# docker ps -a 

CONTAINER ID   IMAGE         COMMAND       CREATED         STATUS                     PORTS     NAMES

0f1c67f488b4   centos        "/bin/bash"   3 minutes ago   Exited (0) 3 seconds ago             cranky_hypatia

ebb882b6736d   hello-world   "/hello"      32 hours ago    Exited (0) 32 hours ago              nice_wright

```

**退出容器**

```shell

exit # 退出容器

Ctrl + P + Q # 容器不停止退出

```

**删除容器**

```shell

docker rm 容器id

docker rm -f $(docker ps -aq) # 删除所有容器

docker ps -aq | xargs docker rm # 删除所用容器 利用管道特性

```

**启动和停止容器**

```shell

docker start 容器id

docker restart 容器id

docker stop 容器id

docker kill 容器id

```



### 常用的其他命令

**后台启动容器**

```sh

[root@192 ~]# docker run -d centos

73adc1fab09efd6a57f7a87d640ea180b826231c78a3795b9329380076b3903b

[root@192 ~]# docker ps 

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

# 命令 docker run -d 容器id会出现ps无法看到容器

# docker容器使用后台运行, 就必须要使用一个前台进程, 如果没有应用, 则自动停止



```

**查看日志命令**

```sh

docker logs

docker logs -ft --tail number 容器id

# 例子 显示后十条日志

docker logs -ft --tail 10 id名

```

> docker run  -d 镜像名  -c "<shell命令>"



**查看容器中的进程信息**

```sh

docker top 容器id #查看容器id的进程信息

```

**查看镜像的元数据**

```sh

docker inspect 容器id # 查看容器id的元数据

```

**进入当前正在运行的容器**

```sh

# 方式一 docker exec

docker exec -it 容器id /bin/bash



# 方式二 docker attach

docker attach 容器id /bin/bash

```

> 二者的区别:

> 	dcoker exec -it 进入操作终端

> 	docker attach 进入正在执行界面, 无法进行操作



**从容器来复制文件到主机**

```sh

docker copy 容器id:容器内的路径 主机路径

[root@192 ~]# docker cp ed34aece5e5d:/home/copy.cpp /home

Successfully copied 1.54kB to /home

```

> 同时, 我们可以使用-v的可选参数, 实现自动同步的功能. v = volume



### 小结

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308010854237.png)

**端口暴露**

![[Drawing 2023-08-01 12.07.12.excalidraw]]



### 可视化

- portainer

```sh

docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer

```

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308021219218.png)



![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308021223315.png)

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308021224373.png)

> portainer可以很方便的查看镜像容器的信息



## Docker镜像讲解



### docker镜像加载原理

> 联合文件系统



分层 轻量 高效的文件系统UnionFS. 其实就是可以通过逐步添加的方式进行更新



**bootfs**主要包含bootloader和kernel. bootloader主要是引导加载kernel, Linux启动就会运行bootfs, 即引导层

**rootfs**就是各种不同操作系统的发行版, 包含基础的命令. 

**bootfs+rootfs**镜像就是更新**rootfs**就可以了.



比如对于一个pull过来的镜像, 当我们实例化后, 就会在镜像层上生成一个容器层, 进行commit的时候会同时进行一次修改

就是相当于在原有的镜像上修改. 镜像为不可更改. 

### commit 镜像

```sh

docker commit 

docker commit -m="提交的描述信息" -a="作者" 容器id name:tag

# 示例

$ docker commit -a="JunDecNo" -m="tomcat initial webapps" tomcat9 tomcati:0.1.1

sha256:27a2df35a29460d6da20c3e3ac77df9d4b22ba9cd67fc36d8b28bfffa492b8cf

[root@192 ~]# docker images

REPOSITORY            TAG       IMAGE ID       CREATED          SIZE

tomcati               0.1.1     27a2df35a294   25 seconds ago   685MB

```



## 容器数据卷



> 存在的问题: 如果将数据都放在容器里, 如果容器删除了那么数据就丢失了

> 	就需要在删除容器的时候数据依旧保存在本地

同时多个容器也可以使用同一个目录,实现数据共享. 这样就可以实现运行和数据分离



**方式一: 使用-v方式**

```sh

-v 宿主机目录:容器目录

#例如

$ docker run -it -v /home/volume:/home/ centos ctos /bin/bash

```

**样例图**

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308031104561.png)

Mysql实战[[Docker作业#作业四 MySql同步]]



### 具名和匿名挂载



**匿名挂载**



```sh

# 匿名挂载

$ docker run -d -p 3310:3306 --name mysql02 -v /etc/mysql/conf.d mysql

```

> 匿名挂载就是直接指定容器内的文件路径.

> 并没有给volume起名字. 那么到底挂载在哪里呢?

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308041457352.png)





**具名挂载**

```sh

# 具名挂载

$ docker run -d -p 3310:3306 --name mysql03 -v name:/etc/mysql/data mysql

```

> 具名挂载就是容器具有具体的名字

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308041459386.png)

```sh

[root@192 mysql]# docker volume inspect centOS

[

    {

        "CreatedAt": "2023-08-04T14:58:52+08:00",

        "Driver": "local",

        "Labels": null,

        "Mountpoint": "/var/lib/docker/volumes/centOS/_data",

        "Name": "centOS",

        "Options": null,

        "Scope": "local"

    }

]

```

> 可以看到docker的卷存放在宿主机的`\var\lib\docker\volumes`中

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308041501338.png)

**总结**

```sh

# 匿名

-v 容器内路径

-v 名称:容器内路径

-v 宿主机绝对路径:容器内路径

```

> 同时可以修改读取权限

> ro 只读

> rw 可读可写



`docker run -v name:path:[ro|rw] centos`

**方式二: 通过DockerFile**

通过脚本构建文件

dockerl file

```sh

FROM centos



VOLUME ["volume01","volume02"] # 匿名挂载



CMD echo "------------end---------------"

CMD /bin/bash

```

docker build

```sh

docker build -f dockerfile1 -t centos .

```

> -t表示构建的name:tag

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308041818028.png)

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308041830265.png)



**数据卷同步数据**

容器与容器之间同步

- 先申明一个容器, 之后再实例化容器时进行绑定`docker run -it --name docker01 --volumes-from docker02 jentos`

> container1 volumes-from container2

> container1就实现了与container2数据共享



数据卷同步在删除container2时container1的共享卷仍然可以运行

*其实就是container2和container1对应宿主机中的同一个挂载卷, 只要宿主机中不删除那就依然可以使用*



这里就有个问题了, 共享卷共享的是哪部分?

针对一样的两个centos系统. 

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308050025225.png)



![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308050025038.png)

> 可以看到挂载的信息时一直的. 

> 所以在使用volumes-from 就是复制container的挂载信息. 所以也会同步这两个文件

> 如果**没有挂载信息那就不存在同步**

> 存在挂载信息, 就会新建目录



```sh

[root@192 ~]# docker run -it --name docker03 --volumes-from docker02 centos

[root@a7298952d2d9 /]# ls

bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01  volume02

```



## DockerFile



**DockerFile时构建Docker image的文件**

docker的构建步骤:

1. 编写一个dockerfile文件

2. docker build 构建镜像

3. docker run 运行镜像

4. docker push 发布镜像



### Docker的构建过程

1. 每个关键字必须大写

2. 顺序执行

3. # 表示注释

4. 每个指令都会提交一个新的镜像层



### DockerFile的指令

![](https://i.bmp.ovh/imgs/2022/03/800e39c60291da56.png)

```sh

CMD          # 指定容器启动后的命令,会被替代

ENTRYPOINT   # 与CMD类似, 但可以追加命令

ONBUILD      # 运行DockerFile就会触发

COPY         # 类似ADD, 拷贝文件

ENV          # 构建的时候设置环境变量

```

[[Docker作业#作业五 实战DockerFile]]

> 可以通过docker history 查看容器构建历史



### CMD和ENTRYPOINT的区别

```sh

FROM centos



CMD ["ls", "-a"]

ENTRYPOINT ["ls", "-a"]

```

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308050145505.png)

> 只有最后一个CMD会生效

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308050200633.png)

> ENTRYPOINT的-l可以追加 而CMD会报错



实战Tomcat[[Docker作业#作业六 实战Tomcat]]

### 发布镜像到dockerhub



```sh

docker login --help

Options:

  -p, --password string   Password

      --password-stdin    Take the password from stdin

  -u, --username string   Username

docker login -u jundecno



WARNING! Your password will be stored unencrypted in /root/.docker/config.json.

Configure a credential helper to remove this warning. See

https://docs.docker.com/engine/reference/commandline/login/#credentials-store



Login Succeeded



[root@192 ~]# docker push jundecno/tomcat:latest

The push refers to repository [docker.io/jundecno/tomcat]

5f70bf18a086: Pushed 

67ffe5d1bc00: Pushed 

67e24e94fed1: Pushed 

74ddd0ec08fa: Pushed 

latest: digest: sha256:0593dbe646f6c41d4b305d5aaf83bab1949f15a7bd893a1e9caa10cbea92f3fd size: 1160

```

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308051634898.png)



### 发布到阿里云

1. 登录阿里云

2. 找到镜像服务

3. 创建命名空间![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308051641869.png)

4. 创建镜像仓库![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308051643244.png)

5. 浏览阿里云![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308051647721.png)

```sh

[root@jundecno ~]$ docker login --username=lucaxzzz registry.cn-hangzhou.aliyuncs.com

# 需要重新命令

[root@192 ~]$ docker tag 39251683c4fd  registry.cn-hangzhou.aliyuncs.com/jundecno/test-jun:1.0

# push

[root@192 ~]$ docker push registry.cn-hangzhou.aliyuncs.com/jundecno/test-jun:1.0

The push refers to repository [registry.cn-hangzhou.aliyuncs.com/jundecno/test-jun]

5f70bf18a086: Pushed 

67ffe5d1bc00: Pushed 

67e24e94fed1: Pushed 

74ddd0ec08fa: Pushed 

1.0: digest: sha256:0593dbe646f6c41d4b305d5aaf83bab1949f15a7bd893a1e9caa10cbea92f3fd size: 1160

```

![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308051658607.png)

> 阿里云的镜像仓库和dokcer hub的上传push类似

> 阿里云真的很不错. 点赞



### 小结



![image.png](https://cdn.jsdelivr.net/gh/jundecno/imagerepo@main/Blogs/MarkDown/202308051703566.png)

## Docker Network



### 理解docker0

```sh

4: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 

    link/ether 02:42:08:37:ab:e4 brd ff:ff:ff:ff:ff:ff

```

> docker如何处理网络连接

```sh

[root@192 ~]# docker run -d -P --name tomcat01 tomcat

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000

    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

    inet 127.0.0.1/8 scope host lo

       valid_lft forever preferred_lft forever

13: eth0@if14: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 

    link/ether 02:42:ac:11:00:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0

    inet 172.17.0.4/16 brd 172.17.255.255 scope global eth0

       valid_lft forever preferred_lft forever

```

> 容器的eth0表示容器的地址



日后再见!



