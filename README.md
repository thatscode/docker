# 内容提要
- docker介绍
- docker安装
- 镜像管理
- 容器管理
- 网络访问
- 数据管理
- 镜像构建
- 私有仓库
- 核心技术
- 生产时间
- 生态圈

## docker介绍
###官网: <http://docker.io/>

###Docker 官方说明
![](http://i.imgur.com/kF9kSVf.png)

- build → ship → run
- 理念：一次构建，多次运行
- docker主要解决资源利用率低的问题
- 并且docker改变了软件的交付方式。
- 目前docker只能应用于linux的64bit的系统。
- 基于linux lxc

###docker的组成：
- docker client
- docker server

###docker的组件：
- 镜像 image → 镜像是只读的
- 容器 container → 由镜像生成
- 仓库 repository  → 集中存放镜像的地方 #管理类似于github，可以直接pull

###Docker 跟 OpenStack的对比

对比类别 | docker | openstack(KVM为例)
---- | ----|----
虚拟化级别 | 内核虚拟化-->是linux内核支持的 | kvm-->硬件虚拟化-->是CPU支持的
部署难度 | 非常简单 | 组件多，部署复杂
启动速度 | 秒级 | 分钟级 
执行性能 | 和物理系统几乎一致 | VM会占用一些资源，大约占用6%
镜像体积 | M级 | G级
管理效率 | 管理简单 | 组件相互依赖，管理复杂
隔离性 | 隔离性高 | 彻底隔离
可管理性 | 单进程，不建议启动SSH | 完整的系统管理
网络连接 | 比较弱 | 借助Neutron可以灵活组建各类网络架构

###Docker 能做什么？
- simplifying configuration         #简化配置
- Developer Productivity            #提高开发效率
- Server Consolidation              #服务整合
- Multi-tenancy                     #多租户的使用场景
- Rapid Deployment                  #快速部署
- Code Pipeline Management          #代码流式管理
- Debugging Capabilities            #开发测试
- App Isolation                     #应用隔离 

###docker 试用的场景：适用于微服务
- 面向产品---->产品交付                 #---->例如有些产品，用户可能只是想安装进行评估，不想有太复杂的安装部署
- 面向开发---->简化环境配置          #
- 面向测试---->多版本测试              #
- 面向运维---->环境一致性              #---->相比StactStack可以实现环境回退
- 面向架构---->自动化扩容(微服务)  #---->
- 面向自动化---->
- 面向微服务---->
- 面向大规模的分布式架构(微信红包)
 
###目前搞Docker 的原因排序： 
- 技术储备（别的公司都在搞，我们不能落下）
- 没技术栈，没技术债
- 跟上节奏，提升自己技术---->装逼（我靠，别的公司都有了。我们不能没有啊）
- 符合当前业务需求

##docker 安装
	Step1，安装epel源

	Step2，yum -y install docker-io  #CentOS 6
	       yum -y install docker     #CentOS 7

##docker操作
###docker 镜像操作
	docker search     ##搜索
	docker pull         ##拉取
	docker images   ##查看
	docker rmi          ##删除---->如果镜像创建了容器，镜像将不能被删除

#每个镜像都有一个唯一的ID
	[root@centos6 ~]# docker search centos
	NAMEDESCRIPTION STARS OFFICIAL   AUTOMATED
	centos  The official build of CentOS.   1171  [OK]  
	......
	省略若干行
	......
	[root@centos6 ~]# 

上面列表表头的内容分别是：镜像的名称，描述，星级，是否为官方镜像，是否是自动构建的。

	[root@ad2 ~]# docker pull centos
	......
	省略若干行
	......
	[root@ad2 ~]# docker images
	REPOSITORY             TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	docker.io/centos       latest              7322fbe74aa5        7 weeks ago         172.2 MB
	darkhuang/phantom198   latest              3c18eb3b7274        7 weeks ago         643.1 MB
	[root@ad2 ~]#

#镜像导出
	[root@docker-01 ~]# docker images
	REPOSITORY           TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	docker.io/centos     latest              60e65a8e4030        44 hours ago        196.6 MB
	docker.io/nginx      latest              813e3731b203        9 days ago          133.8 MB
	docker.io/registry   latest              a8706c2bfd21        2 weeks ago         422.8 MB
	[root@docker-01 ~]#
	[root@docker-01 ~]# docker save centos > /opt/centos.tar.gz
	[root@docker-01 ~]# ll -h !$
	ll -h /opt/centos.tar.gz
	-rw-r--r-- 1 root root 195M Dec 26 19:33 /opt/centos.tar.gz
	[root@docker-01 ~]# 

#docker 容器管理
     docker run --name -h hostname    ##启动容器
     docker stop CONTAINER-ID         ##停止容器
     docker ps                        ##查看活动容器
     docker ps -a                     ##查看所有容器
     docker ps -l                     ##查看最后运行的容器
     docker exec | docker attach      ##进入容器
     docker rm                        ##删除容器

# 运行容器执行echo命令，命令执行完成之后，容器会马上退出
	[root@docker-01 ~]# docker run centos /bin/echo "hehe"
	hehe
	[root@docker-01 ~]#
	[root@docker-01 ~]# docker ps  -a
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
	33f548ab24be        centos              "/bin/echo hehe"    3 seconds ago       Exited (0) 2 seconds ago                       desperate_kowalevski
	[root@docker-01 ~]#
#运行容器执行/bin/bash命令，命令执行后，会进入容器的交互界面：
	[root@ad2 ~]# docker run --name mydocker -i -t centos /bin/bash
	[root@bf55a4fd6e73 /]#
	[root@bf55a4fd6e73 /]# ls
	bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
	[root@bf55a4fd6e73 /]# ps  -ef
	UID        PID  PPID  C STIME TTY          TIME CMD
	root         1     0  0 03:10 ?        00:00:00 /bin/bash
	root        19     1  0 03:11 ?        00:00:00 ps -ef
	[root@bf55a4fd6e73 /]#

※注意事项※
     #①  docker 容器会运行指定的一个应用进程，当应用程序退出之后，容器也就会退出。
     #②  容器里的进程需要是前台运行的，如果是后台运行的docker会认为容器的任务已经执行完成，就会退出。 
     #③  docker run 命令不同，容器在运行程序后的处理结果不同：
                  运行echo "hello world",在运行后容器就退出了；
                  运行/bin/bash后，容器还在运行，不退出。

	[root@ad2 ~]# docker ps -a
	CONTAINER ID        IMAGE                         COMMAND                CREATED             STATUS                        PORTS               NAMES
	bf55a4fd6e73        centos:latest                 "/bin/bash"            3 minutes ago       Exited (0) 7 seconds ago                          mydocker           
	391a7d64176f        centos:latest                 "/bin/echo 'Hello Wo   13 minutes ago      Exited (0) 13 minutes ago                         happy_fermat       
	2912f5b3981e        darkhuang/phantom198:latest   "node index.js"        3 weeks ago         Exited (137) 25 minutes ago                       prickly_brown      
	[root@ad2 ~]# docker start  bf55a4fd6e73  ##start 运行/bin/bash的容器
	bf55a4fd6e73


	[root@ad2 ~]# docker ps  ##查看，运行/bin/bash的容器一直在，没有退出
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
	bf55a4fd6e73        centos:latest       "/bin/bash"         4 minutes ago       Up 2 seconds                            mydocker           
	[root@ad2 ~]# docker ps
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
	bf55a4fd6e73        centos:latest       "/bin/bash"         6 minutes ago       Up About a minute                       mydocker           
	[root@ad2 ~]# docker start  391a7d64176f  ##start 运行echo hello world的容器
	391a7d64176f
	[root@ad2 ~]# docker ps   ##查看还是只有运行/bin/bash的容器
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
	bf55a4fd6e73        centos:latest       "/bin/bash"         7 minutes ago       Up 2 minutes                            mydocker           
	[root@ad2 ~]#

###daemon方式运行docker
     docker run -d --name mydocker1 centos 
进入容器 
     方法①  docker attach 容器ID
     方法②  nsenter 容器PID   
nsenter的安装方式
     yum  -y install  util-linux
     nsenter是访问另一个进程的namespace的方式。

     docker ps             ##查看容器ID
     docker attach       ##连接到容器
	[root@ad2 ~]# docker ps  -l
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
	bf55a4fd6e73        centos:latest       "/bin/bash"         20 minutes ago      Up 15 minutes                           mydocker           
	[root@ad2 ~]# docker attach bf55a4fd6e73
	[root@bf55a4fd6e73 /]#
	[root@bf55a4fd6e73 /]# ls
	bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
	dev  home  lib64  media       opt  root  sbin  sys  usr
	[root@bf55a4fd6e73 /]# 


     docker inspect --format "{{.State.Pid}}" <容器名称>                     ##→获取容器PID
     nsenter --target 32439 --mount --uts --ipc --net --pid            ##→通过容器PID进入容器


	[root@ad2 ~]# docker inspect --format "{{.State.Pid}}" myNginx
	25360
	[root@ad2 ~]# nsenter --target 25360 --mount --uts --ipc --net --pid
	root@36e7367300d2:/# 
##详细过程
	[root@ad2 ~]# docker ps 
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
	[root@ad2 ~]# docker ps  -a
	CONTAINER ID        IMAGE                         COMMAND                CREATED             STATUS                        PORTS               NAMES
	36e7367300d2        nginx:latest                  "nginx -g 'daemon of   3 minutes ago       Exited (0) 34 seconds ago                         myNginx            
	bf55a4fd6e73        centos:latest                 "/bin/bash"            30 minutes ago      Exited (0) 5 minutes ago                          mydocker           
	2912f5b3981e        darkhuang/phantom198:latest   "node index.js"        3 weeks ago         Exited (137) 52 minutes ago                       prickly_brown      
	[root@ad2 ~]#
	[root@ad2 ~]# docker start 36e7367300d2
	36e7367300d2
	[root@ad2 ~]# docker  ps -l
	CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
	36e7367300d2        nginx:latest        "nginx -g 'daemon of   3 minutes ago       Up 8 seconds        80/tcp, 443/tcp     myNginx            
	[root@ad2 ~]#
	[root@ad2 ~]# docker inspect --format "{{.State.Pid}}" myNginx
	25360
	[root@ad2 ~]# nsenter --target 25360 --mount --uts --ipc --net --pid
	root@36e7367300d2:/#
	root@36e7367300d2:/# ls
	bin  boot  dev     etc  home  lib     lib64  media  mnt  opt     proc  root  run  sbin  srv  sys  tmp  usr  var
	root@36e7367300d2:/# ls  /etc/nginx/
	conf.d     fastcgi_params     koi-utf  koi-win  mime.types  nginx.conf  scgi_params  uwsgi_params  win-utf
	root@36e7367300d2:/#

##编写进入容器的小脚本  in.sh
	[root@ad2 scripts]#
	#!/bin/bash
	CNAME=$1
	CPID=$(docker inspect --format "{{.State.Pid}}" $CNAME) 
	nsenter --target "$CPID" --mount --uts --ipc --net --pid 
	[root@ad2 scripts]# vim  in.sh
	[root@ad2 scripts]# chmod +x in.sh
	[root@ad2 scripts]# ./in.sh myNginx
	root@36e7367300d2:/# exit
	logout
	[root@ad2 scripts]# 

###docker 网路访问
- 随机端口映射：
	docker run -P

- 指定端口映射：
	-p hostPort:containerPort               ##指定宿主机端口映射
	-p ip:hostPort:containerPort            ##指定宿主机ip端口映射
	-p ip:hostPort:containerPort:udp        ##指定宿主机ip端口映射,并指定协议
	-p ip::containerPort                    ##宿主机容器端口一对一映射

- 随机映射

	docker run  -d  -P  --name mynginx1 nginx

启动一个nginx容器，并映射随机端口
[root@ad2 scripts]# docker run  -d  -P  --name mynginx1 nginx
aea5d568f54f00d2cb837780bf5aeed8a86ede7aca2f7aee889752682c2ff6e2
[root@ad2 scripts]#
[root@ad2 scripts]# docker ps  -l
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                                           NAMES
aea5d568f54f        nginx:latest        "nginx -g 'daemon of   9 seconds ago       Up 7 seconds        0.0.0.0:32768->80/tcp, 0.0.0.0:32769->443/tcp   mynginx1           
[root@ad2 scripts]# 

#2：映射指定端口
     docker run -d -p 91:80 mynginx2 nginx
     docker run -d -p 81:80 --name mynginx2 nginx

docker的数据管理
docker  目录的创建
    创建目录 data
	[root@ad2 ~]# docker ps
	CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                                           NAMES
	aea5d568f54f        nginx:latest        "nginx -g 'daemon of   2 hours ago         Up 2 hours          0.0.0.0:32768->80/tcp, 0.0.0.0:32769->443/tcp   mynginx1           
	36e7367300d2        nginx:latest        "nginx -g 'daemon of   2 hours ago         Up 2 hours          80/tcp, 443/tcp                                 myNginx            
	[root@ad2 ~]#
	[root@ad2 ~]# docker ps -l
	CONTAINER ID        IMAGE               COMMAND                CREATED              STATUS                          PORTS               NAMES
	38aa5b3221f4        nginx:latest        "nginx -g 'daemon of   About a minute ago   Exited (0) About a minute ago                       volume-test1       
	[root@ad2 ~]# ls  /
	bin   dev  home  lib64  mnt  proc  run   server  sys  usr
	boot  etc  lib   media  opt  root  sbin  srv     tmp  var

# 数据卷的访问
	[root@ad2 ~]# docker  run  -it  --name volume-test-1 -h nginx -v /data  centos
	[root@nginx /]#
	[root@nginx /]# ls
	bin   dev  home  lib64       media  opt   root  sbin  sys  usr
	data  etc  lib   lost+found  mnt    proc  run   srv   tmp  var
	[root@nginx /]#
	[root@nginx /]# ls data/
	test-file
	[root@nginx /]# 

# 查看容器volume
     docker inspect -f {{.Volumes}} <容器名称>
	[root@ad2 ~]# docker ps  -l
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
	ef061a196b7a        centos:latest       "/bin/bash"         35 seconds ago      Up 33 seconds                           volume-test-1      
	[root@ad2 ~]#
	[root@ad2 ~]# docker inspect -f {{.Volumes}} volume-test1
	map[/var/cache/nginx:/var/lib/docker/vfs/dir/334c9eda2e7e7f98a426af3859f80c62c31027e4de2ccfe2245c9f4927927282 /data:/var/lib/docker/vfs/dir/abb98d118fe961ba159b62cc319276d382a8d8bbb739bf744341dd4d3c99c853]
	[root@ad2 ~]# docker inspect -f {{.Volumes}} volume-test-1
	map[/data:/var/lib/docker/vfs/dir/a159c91e7e067d39a6de0f2fa7d7426d6b8b52ce04ec12c39d40368648f1a2c7]
	[root@ad2 ~]# cd  /var/lib/docker/vfs/dir/a159c91e7e067d39a6de0f2fa7d7426d6b8b52ce04ec12c39d40368648f1a2c7
	[root@ad2 a159c91e7e067d39a6de0f2fa7d7426d6b8b52ce04ec12c39d40368648f1a2c7]# pwd
	/var/lib/docker/vfs/dir/a159c91e7e067d39a6de0f2fa7d7426d6b8b52ce04ec12c39d40368648f1a2c7
	[root@ad2 a159c91e7e067d39a6de0f2fa7d7426d6b8b52ce04ec12c39d40368648f1a2c7]# touch test-file
	[root@ad2 a159c91e7e067d39a6de0f2fa7d7426d6b8b52ce04ec12c39d40368648f1a2c7]#

※docker 日志存储的短板※
     目录映射到物理文件里，再收集物理主机上的日志文件。

	[root@ad2 ~]# docker run -it --name volume-test2 -h nginx -v /opt:/opt centos
	[root@nginx /]#
	[root@nginx /]# ls
	bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
	dev  home  lib64  media       opt  root  sbin  sys  usr
	[root@nginx /]# ls  /opt/
	[root@nginx /]# 

只读挂载
     docker run -it --name volume-test2 -h nginx -v /opt:/opt:ro centos
挂载之前容器的volume，即使之前的容器是停止状态也不会影响挂载。
     docker run -it --name volume-test4 --volumes-from volume-test1 centos
docker镜像构建
     方法①  手动构建：
     方法②  docker file
#1，手动构建一个nginx镜像
     #运行并进入容器
     docker run --name nginx-man -it centos
     #安装nginx的依赖
     yum install -y wget gcc gcc-c++ make openssl-devel
     #安装pcre，安装nginx
     #修改nginx的配置文件，添加"daemon off；"配置，使nginx为前台运行：
           [root@6645d5483c38 local]# head -1 nginx/conf/nginx.conf
           daemon off;
           [root@6645d5483c38 local]#
#2，docker file
     · 基础镜像信息
     ·  维护者信息
     ·  镜像操作命令
     ·  容器启动时执行指令

docker的资源隔离  LXC
Kernel  namespace
Pid
net
ipc   （进程间的交互方式）
mnt （）
uts
user
docker的资源限制 cgroup
CPU 内存 (磁盘暂时还需要手动)
docker容器默认使用1024个cpu配额
cpu压测，构建stress容器进行压测

#Dockerfile
    FROM centos
    ADD epel-6.repo /etc/yum.repos.d/
    RUN yum -y install stress && yum clean all
    ENTRYPOINT ["stress"]

#Build
    docker build -t stress .

# RUN
    docker run -it --rm stress --cpu 1

#RUN 并制定cpu配额 stress
    docker run -it --rm -c 512 stress --cpu 1
    docker run -it --rm --cpuset=0 stress --cpu 1

#内存资源限制 -m
    docker run -it --rm -m 128m stress --vm 1 --vm-bytes 120m --vm-hang 0
    docker-compose  Fig

#可以实现多容器同时构建
##docker registry

###需要解决https的问题
- 买证书
- 使用--inscure-registry
- nginx 做转发

###registry的使用步骤

1. 构建
2. 打tag
3. push


docker ui   #shipyard

shipyard <http://shipyard-project.com/docs/quickstart/>

赵班长(57459267) 2015/8/8 18:05:38

	docker run -d -p 5001:5000 registry

赵班长(57459267) 2015/8/8 18:06:45

	docker tag elasticsearch 192.168.199.220:5001/test/es:v1

赵班长(57459267)  18:26:58

	other_args="-H tcp://0.0.0.0:235 -H unix:///var/run/docker.sock"

####w
	[root@ad2 ~]# docker run  -d  -p 5000:5000 registry
	683e1deab7a79821c5105b1d5226dedb8b10810c517c9818149325679e51b872
	[root@ad2 ~]#
	[root@ad2 ~]# docker ps
	CONTAINER ID        IMAGE                 COMMAND                CREATED             STATUS              PORTS                NAMES
	202199e48779        stephen/my-nginx:v2   "/usr/local/nginx/sb   2 hours ago         Up 2 hours          0.0.0.0:99->80/tcp   elated_meitner     
	[root@ad2 ~]#
	[root@ad2 ~]# docker ps  -l
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
	683e1deab7a7        registry:latest     "docker-registry"   12 seconds ago      Exited (1) 7 seconds ago                       lonely_wright      
	[root@ad2 ~]# docker start 683e1deab7a7
	683e1deab7a7
	[root@ad2 ~]#
	[root@ad2 ~]# docker ps 
	CONTAINER ID        IMAGE                 COMMAND                CREATED             STATUS              PORTS                    NAMES
	683e1deab7a7        registry:latest       "docker-registry"      34 seconds ago      Up 4 seconds        0.0.0.0:5000->5000/tcp   lonely_wright      
	202199e48779        stephen/my-nginx:v2   "/usr/local/nginx/sb   2 hours ago         Up 2 hours          0.0.0.0:99->80/tcp       elated_meitner     
	[root@ad2 ~]#
# registry
	[root@docker-01 nginx]# docker run  -d  -p 5000:5000 registry
	b1552cabd72fd0afa00718d168d81260ff3a8de405b1db0d13bfb4b76f7673d7
	[root@docker-01 nginx]#


https慢---->ssl卸载












