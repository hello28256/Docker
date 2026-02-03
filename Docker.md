# Docker



```shell
yangq@Ubuntu24:/$ docker info
Client: Docker Engine - Community
 Version:    28.2.2
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.24.0
    Path:     /usr/libexec/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v5.0.2
    Path:     /usr/libexec/docker/cli-plugins/docker-compose

Server:
 Containers: 3
  Running: 3
  Paused: 0
  Stopped: 0
 Images: 5
 Server Version: 28.2.2
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Using metacopy: false
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 CDI spec directories:
  /etc/cdi
  /var/run/cdi
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 05044ec0a9a75232cad458027ca83437aae3f4da
 runc version: v1.2.5-0-g59923ef
 init version: de40ad0
 Security Options:
  apparmor
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.8.0-60-generic
 Operating System: Ubuntu 24.04.2 LTS
 OSType: linux
 Architecture: x86_64
 CPUs: 2
 Total Memory: 1.575GiB
 Name: Ubuntu24.04
 ID: e8a24da6-4b27-4428-b3e6-1c9c2122845e
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Experimental: false
 Insecure Registries:
  ::1/128
  127.0.0.0/8
 Registry Mirrors:
  https://1ryk4xff.mirror.aliyuncs.com/
 Live Restore Enabled: false
```





## docker查看信息

```shell
docker info								//docker的详细信息，包括镜像，版本等
docker version						//查看docker的版本信息
docker 命令 --help				//	查看命令帮助文档
```



## docker镜像

```shell
docker images							//查看所有镜像
docker pull 镜像名[:tag]		//不写tag拉取最新的
docker rmi id							//删除镜像  -f 强制删除正在运行的镜像
docker search	mysql				//搜索镜像命令
  # 可选项，通过搜索赖过滤
  --filter=STARS=3000    # 搜索出来的镜像就是STARS大于3000的   
```



## docker容器基本操作

```shell
docker ps 		//查看正在运行的容器
docker ps -a	//查看所有容器，包括停止的容器
exit   				//前台方式的话，退出就停止，后台不会
Ctrl + P + Q  //前台方式进入，这样退出不会停止
docker rm id	//删除停止的容器
docker rm -f id	//强制删除运行的容器
docker kill id	//潜质杀死容器
docker start id      //启动容器
docker restart id    //重启容器
docker run -d centos	//启动后又停止了，容器使用后台运行，就必须有要一个前台进行，docker发现没有应用，就会自动的停止，有些镜如nginx没有启动CMD，必须加/bin/bash，启动命令
docker logs id -f			//查看日志，	-f 跟踪日志输出
docker top id 	//查看容器中的进程信息
docker inspect id/镜像id //查看镜像的元数据
docker exec -it 容器id mysql -uroot -p	//进入正在运行的id
docker attach id 	//进入容器正在执行的终端，不会启动新的进程！
```

## 提交镜像

```shell
docker commit -a='作者' -m='备注' id 新镜像名
root@Ubuntu24:~# docker commit -a='yangq' -m='add webapps' e95 tomcat_test
sha256:cebb9524dfbddefb9cd6341f7e162977483333e47d4914dcbdd3703fefd5b006
root@Ubuntu24:~# docker ps
CONTAINER ID   IMAGE          COMMAND             CREATED         STATUS         PORTS                                         NAMES
e95af8a87b73   fb5657adc892   "catalina.sh run"   8 minutes ago   Up 8 minutes   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp   tomcat_test
root@Ubuntu24:~# docker iamges
docker: unknown command: docker iamges

Run 'docker --help' for more information
root@Ubuntu24:~# docker images
REPOSITORY                                                                          TAG       IMAGE ID       CREATED          SIZE
tomcat_test                                                                         latest    cebb9524dfbd   12 seconds ago   684MB
tomcat                                                                              latest    89301ea70d99   40 seconds ago   684MB
crpi-zqmddwfip4cwc7n1.cn-beijing.personal.cr.aliyuncs.com/learning-notes/frontend   latest    7847709502d8   4 days ago       63.6MB
crpi-zqmddwfip4cwc7n1.cn-beijing.personal.cr.aliyuncs.com/learning-notes/backend    latest    44f3a19312c0   4 days ago       472MB
nginx                                                                               latest    605c77e624dd   4 years ago      141MB
tomcat                                                                              9.0       b8e65a4d736d   4 years ago      680MB
tomcat                                                                              <none>    fb5657adc892   4 years ago      680MB
mysql                                                                               8.0       3218b38490ce   4 years ago      516MB
ubuntu                                                                              latest    ba6acccedd29   4 years ago      72.8MB
centos                                                                              latest    5d0da3dc9764   4 years ago      231MB
```

## 容器数据卷

```shell
docker run -it -v 主机目录:容器目录  //会将这个容器目录下的内容放到主机的目录下
root@Ubuntu24:~# docker run -it -v /home/test:/home centos /bin/bash
[root@4524ce9dfe14 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@4524ce9dfe14 /]# cd home/
[root@4524ce9dfe14 home]# ls
[root@4524ce9dfe14 home]# vi hello.java
[root@4524ce9dfe14 home]# exit
exit
root@Ubuntu24:~# ls
aaa     docker-apps  Dockerfile  learning-notes         passwd   qyang_20250417_180d.cert.pem  root
app.py  dockerfile   Downloads   learning-notes.tar.gz  py_test  qyang_20250417_180d.p12
root@Ubuntu24:~# cd /home
root@Ubuntu24:/home# ls
test  yangq
root@Ubuntu24:/home# cd test/
root@Ubuntu24:/home/test# ls
hello.java
```

### mysql8.0

```shell
docker run -d \
  --name mysql_test_v \
  -p 127.0.0.1:3306:3306 \
  -e MYSQL_ROOT_PASSWORD='StrongPass_ChangeMe!' \
  -v mysql_test_V:/var/lib/mysql \
  mysql:8.0
```

==错误==

``` shell
[root@VM-4-8-centos test]# docker run -d -p 3306:3306 -v /www/mysql:/etc/mysql/conf.d 
-v /www/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=456789 --name mysql01 mysql:5.7
//两个数据卷都挂在到宿主主机的统一个目录下，会导致容器中的etc/mysql/conf.d和/var/lib/mysql数据一样
//挂载卷 = 文件系统共享
```



### 具名和匿名挂载

```shell
docker run -d --name nginx01 -v /etc/nginx nginx	//匿名挂载
docker run -d --name nginx01 -v nginx:/etc/nginx nginx		//具名挂载
```

| 特性                           | 绑定挂载（Bind Mount）             | 具名挂载（Named Volume）                                  | 匿名挂载（Anonymous Volume）                               |
| ------------------------------ | ---------------------------------- | --------------------------------------------------------- | ---------------------------------------------------------- |
| 定义方式                       | `-v /宿主机/路径:/容器/路径`       | `-v my_name:/容器/路径` 或 `docker volume create my_name` | `-v /容器/路径` 或 Dockerfile 中 `VOLUME ["/path"]`        |
| 数据位置                       | 宿主机任意目录（如 `/www/data`）   | Docker 管理目录： `/var/lib/docker/volumes/my_name/_data` | Docker 管理目录： `/var/lib/docker/volumes/<随机ID>/_data` |
| 名称是否可读                   | 路径由用户指定，清晰明确           | 用户命名（如 `mysql_data`），可读性强                     | 随机 UUID，无法识别用途                                    |
| 是否需预先创建                 | 宿主机目录可不存在（但建议存在）   | 可自动创建，也可手动创建                                  | 自动创建，无法手动命名                                     |
| 能否被 `docker volume ls` 列出 | ❌ 否                               | ✅ 是                                                      | ✅ 是（但名字是乱码）                                       |
| 生命周期管理                   | 与宿主机文件系统一致               | 独立于容器，需手动删除 `docker volume rm my_name`         | 容器删除后仍保留，需 c 清理                                |
| 安全性                         | ⚠️ 高风险（可挂载 `/etc`、`/root`） | ✅ 安全（隔离在 Docker 目录）                              | ✅ 安全（同 Named Volume）                                  |
| 跨平台/迁移性                  | ❌ 差（依赖宿主机路径）             | ✅ 好（Docker 抽象层）                                     | ✅ 好（但难识别）                                           |
| 典型用途                       | 配置文件、源代码、日志             | 数据库数据、应用持久化存储                                | 不推荐主动使用，多为 Docker 自动创建                       |

- 数据卷(有两种方式：匿名和具名挂载)属于Docker管理，绑定挂载属于用户管理

  - 绑定挂载主要用于开发时同步代码

    > docker volume ls //识别不到绑定挂载

  - 数据卷的话主要用于数据持久化

    > docker volume prune		//删除没有绑定容器的数据卷
    >
    > docker volume ls				//可以识别到的就是数据卷
    >
    > docker volume inspect name 		//查看数据卷详情
    >
    > docker volume create name			//显示创建数据卷

- 匿名挂载 （难识别、安全性好）
  - 可以通过docker volume ls 识别到
  - 通常由 Dockerfile 或简写命令触发、运行是只写了-v /data
  - 独立于容器

![image-20260202122619490](https://oss-pai-y1p7zrgkn9rww3n1nb-cn-shanghai.oss-cn-shanghai.aliyuncs.com/image-20260202122619490.png)

- 具名挂载（易识别、安全性好）
  - 可以通过docker volume ls识别到
  - `-v my_name:/容器/路径` 或 `docker volume create my_name` 可以主动创建，也可以被动创建卷名
  - 删除容器后，重新启动数据仍然保留

![image-20260203100542706](https://oss-pai-y1p7zrgkn9rww3n1nb-cn-shanghai.oss-cn-shanghai.aliyuncs.com/image-20260203100542706.png)

- 绑定挂载

![image-20260202122657766](https://oss-pai-y1p7zrgkn9rww3n1nb-cn-shanghai.oss-cn-shanghai.aliyuncs.com/image-20260202122657766.png)

```shell
//查看volume的情况,匿名挂载会随机分配一个volume name,如下图
//所有的docker容器内的卷，没有指定目录的情况下都是在/var/lib/docker/volumes/VOLUME NAME/_data
root@Ubuntu24:~# docker volume ls
DRIVER    VOLUME NAME
local     4d7c0eda3e02c79f49298398fd39843cf1cc4be478395b35983fe251d7d7e22a
local     262d2b37c5ee4eff7ffe2c228d8669c876b127c94f0c85466ec50352c0a8e876
local     0442a0f63cc48897522de47eb1aafa459067caa359ffa3fb49e43c1a5cc6cea5
local     aa3c887f5be3c6130871271c6a2fac744729c9bc0da97b552a76d43e6a0b7f1e
local     cd7f1aac8b25a721efaafec62d150c53ad9c393544c63a66ac9448951f83aae8
local     d03accd62c7d628fdb785a9f2c5effed542147995f1f1577486a7f844b45accf
local     d23e3f5bdd097a6423095d0abccc4b9e6c7cb2f7f9f453b2740df441e7dd922e
local     fd9826d1dfd28bb5edaa492c9b3fbc08e200b1928464384f82f0aeae14df2740
local     learning-notes_mysql_data
local     learning-notes_uploads_data
local     maybe_app-storage
local     maybe_postgres-data
local     maybe_redis-data
```

![image-20260202123130335](https://oss-pai-y1p7zrgkn9rww3n1nb-cn-shanghai.oss-cn-shanghai.aliyuncs.com/image-20260202123130335.png)

```
# :ro 表示只读,说明这个路径只能通过宿主机来操作,容器内部无法操作
# :rw 表示可读可写
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx
```

### Dockerfile挂载

```
#Dockerfile
FROM centos

VOLUME ["valume01","volume02"]  #匿名挂载

CMD echo "---end----"

CMD /bin/bash
```

```
docker build -f dockerfile -t mycentos .
```

![image-20260202175431203](https://oss-pai-y1p7zrgkn9rww3n1nb-cn-shanghai.oss-cn-shanghai.aliyuncs.com/image-20260202175431203.png)

### 数据卷容器

#### ==--volumes-from==

![image-20260202192914260](https://oss-pai-y1p7zrgkn9rww3n1nb-cn-shanghai.oss-cn-shanghai.aliyuncs.com/image-20260202192914260.png)

```shell
root@Ubuntu24:~/Mydocker# docker run -d -p 3306:3306 --name mysql01 -e MYSQL_ROOT_PASSWORD=456789 -v /www/mysql/test_mysql:/etc/mysql/conf.d mysql:5.7
ca18b48c64f9d974259b9ab1cb4bbdf9238ebc927918f2f10ba8a5f82fe11af9
root@Ubuntu24:/www/mysql/test_mysql# docker run -d -p 3307:3306 --name mysql02 -e MYSQL_ROOT_PASSWORD=456789 --volumes-from mysql01 mysql:5.7
root@Ubuntu24:/www/mysql/test_mysql# docker run -d -p 3307:3306 --name mysql02 -e MYSQL_ROOT_PASSWORD=456789 --volumes-from mysql01 mysql:5.7
16c90b79ad8b460646ff346717e6526f061abbbd4e7e339b6cde289391c43dec
root@Ubuntu24:/www/mysql/test_mysql# docker exec -id mysql02 /bin/bash
root@Ubuntu24:/www/mysql/test_mysql# docker exec -it mysql02 /bin/bash

```

## Dockerfile

构建步骤：

1、编写一个dockerfile文件

2、doucker build 构建成为一个镜像

3、docker run 运行镜像

4、docker push 发布镜像（DockerHbu、阿里云镜像仓库）

<img src="https://oss-pai-y1p7zrgkn9rww3n1nb-cn-shanghai.oss-cn-shanghai.aliyuncs.com/image-20260202193536890.png" alt="image-20260202193536890" style="zoom: 33%;" />

### Dockerfile指令

```
FROM      ：指定基础镜像。
MAINTAINER：指定镜像的作者和联系方式。
RUN       ：在镜像中执行命令，用于安装和配置应用程序。
CMD       ：指定容器启动时要运行的命令，可以被 Docker run 命令覆盖。
ENTRYPOINT：指定容器启动时要运行的命令，不会被 Docker run 命令覆盖。而是追加。
EXPOSE    ：声明容器运行时监听的端口。
ENV       ：设置环境变量。
ADD       ：将文件复制到镜像中。
COPY      ：将文件复制到镜像中，与 ADD 的区别是 COPY 只支持本地文件，而 ADD 可以从远程 URL 下载并解压文件。
VOLUME    ：在镜像中创建一个挂载点，用于持久化数据。
WORKDIR	  ：镜像的工作目录
ONBUILD   ：当构建一个被继承 DockerFile 这个时候就会运行 ONBUILD 的指令。触发指令。
```

- 构建一个mycentos

```
# 1、编写dockerfile文件
FROM centos:7

RUN sed -i 's|^mirrorlist=|#mirrorlist=|g' /etc/yum.repos.d/CentOS-Base.repo && \
    sed -i 's|^#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Base.repo

RUN yum clean all && \
    yum makecache && \
    yum -y install vim net-tools
MAINTAINER cong

ENV MYPATH /user/local
WORKDIR $MYPATH


EXPOSE 80

CMD echo $MYPATH
CMD echo "----end------"
CMD /bin/bash
```

### 实战构建-获取jdk和tomcat的包

- 获取jdk和tomcat包

```shell
 wget https://packages.baidu.com/app/jdk-8/jdk-8u121-linux-x64.tar.gz
 wget https://mirrors.cnnic.cn/apache/tomcat/tomcat-9/v9.0.72/bin/apache-tomcat-9.0.72.tar.gz 
```

```shell
FROM centos:7

# 指定作者信息
MAINTAINER cong<110@qq.com>

# 拷贝本地文件到镜像
COPY readme.txt /usl/local

# 将本地文件加到镜像中并解压
ADD jdk-8u121-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.72.tar.gz /usr/local/

# 给镜像安装vim命令
# RUN yum install -y vim
RUN sed -i 's|^mirrorlist=|#mirrorlist=|g' /etc/yum.repos.d/CentOS-Base.repo && \
    sed -i 's|^#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Base.repo

RUN yum clean all && \
    yum makecache && \
    yum -y install vim net-tools

# 指定工作目录
ENV MYPATH /usr/local
WORKDIR $MYPATH

# 配置环境变量
ENV JAVA_HOME /usr/local/jdk1.8.0_121
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.72
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.72
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

# 暴露端口
EXPOSE 8080

# 执行启动命令和查看日志
CMD /usr/local/apache-tomcat-9.0.72/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.72/bin/logs/catalins.out
```

- 构建镜像

```shell
docker build -t mytomcat01 .
```

- 运行

```shell
docker run -d -p 8080:8080 --name congtomcat -v /www/tomcat/test:/usr/local/apache-tomcat-9.0.72/webapps/test/ -v /www/tomcat/logs/:/usr/local/apache-tomcat-9.0.72/logs/ 镜像id
```

- test下添加WEB-INF/web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
    id="WebApp_ID" version="2.5">
    
</web-app>
```

- test下田间index.jsp

```jsp
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
    <title>snow</title>
    <style>
        body,html{
            margin: 0;
            padding: 0;
            overflow: hidden;
            height: 100%;
            background: black;
        }
        .snow{
            background: white;
            position: absolute;
            width: 20px;
            height: 20px;
            border-radius: 50%;
        }
    </style>
 
</head>
<body>
    <script>
        //获取屏幕宽高
        var windowWidth = window.screen.width;
        var windowHeight = window.screen.height;
 
        //创建雪花
        function createSnow(){
            var left = 0;
            var top = 0;
 
            //定义一个初始化随机数,使雪花在屏幕中
            var left_random = Math.random() * windowWidth;
            var top_random = Math.random()* windowHeight;
            var div = document.createElement('div');
            div.className = 'snow';
            div.style.transform = 'scale('+(Math.random())+')'
 
            document.body.appendChild(div);
 
            //雪花飘落
            setInterval(function () {
 
                div.style.left = left_random + left +'px';
                div.style.top = top_random + top +'px'
                left += 0.2;
                top += 0.2;
 
                //如果雪花跑到屏幕外面了,让雪花重新返回屏幕顶部
                if(left_random + left >= windowWidth){
                    left_random = Math.random();
                    left = 0;
                }
 
                if(top_random + top >= windowHeight){
                    top_random = Math.random();
                    top = 0;
                }
            },10)
 
        }
        for(var i = 0 ; i < 200 ; i++){
            createSnow()
        }
    </script>
</body>
</html>

```

- 访问：http://182.92.177.191:8080/test/

![image-20260202212453250](https://oss-pai-y1p7zrgkn9rww3n1nb-cn-shanghai.oss-cn-shanghai.aliyuncs.com/image-20260202212453250.png)

发布镜像到阿里云容器镜像仓库





## springboot微服务打包Docker镜像

1.随便找一个springboot项目，然后打包

![image-20260203175857855](https://oss-pai-y1p7zrgkn9rww3n1nb-cn-shanghai.oss-cn-shanghai.aliyuncs.com/image-20260203175857855.png)

2.编写Dockerfile

```shell
FROM eclipse-temurin:21-jre-jammy

COPY *.jar /app.jar

CMD ["--server.port=8080"]   //命令行优先级高于配置文件

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "/app.jar"]
```

3.放到同一个目录下

4.打包

```shell
docker build -t springboot:0.1 .
```

5.启动

```shell
 docker run -d -p 8082:8080 springboot
```

