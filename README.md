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
- 面向产品---->产品交付，例如有些产品，用户可能只是想安装进行评估，不想有太复杂的安装部署
- 面向开发---->简化环境配置
- 面向测试---->多版本测试  
- 面向运维---->环境一致性,相比StactStack等自动化运维工具，docker可以实现环境回退
- 面向架构---->自动化扩容(微服务)
- 面向自动化---->
- 面向微服务---->
- 面向大规模的分布式架构(微信红包)
 
###目前搞Docker 的原因排序： 
- 技术储备（别的公司都在搞，我们不能落下）
- 没技术栈，同时没技术债
- 跟上节奏，提升自己技术---->装逼（我靠，别的公司都有了。我们不能没有啊）
- 符合当前业务需求

##docker 安装
###CentOS
	Step1,安装epel源

	Step2,安装
		CentOS 6
			yum -y install docker-io
		CentOS 7
			yum -y install docker

更多信息，请查看官方文档：
<https://docs.docker.com/engine/installation/linux/centos/>

###Ubuntu
	Step1,添加repo源
		Ubuntu Precise 12.04 (LTS)
			deb https://apt.dockerproject.org/repo ubuntu-precise main
		Ubuntu Trusty 14.04 (LTS)
			deb https://apt.dockerproject.org/repo ubuntu-trusty main
		Ubuntu Wily 15.10
			deb https://apt.dockerproject.org/repo ubuntu-wily main
		Ubuntu Xenial 16.04 (LTS)
			deb https://apt.dockerproject.org/repo ubuntu-xenial main

	Step2,安装
		apt-get install docker-engine

更多信息，请查看官方文档：
<https://docs.docker.com/engine/installation/linux/ubuntulinux/>

##docker操作
###docker 镜像操作
	docker search     ##搜索
	docker pull       ##拉取
	docker images     ##查看
	docker rmi        ##删除---->如果镜像创建了容器，镜像将不能被直接删除，使用-f参数可强制删除(不建议如此操作)

####搜索镜像 
	[root@centos6 ~]# docker search centos
	NAME DESCRIPTION STARS OFFICIAL   AUTOMATED
	centos  The official build of CentOS.   1171  [OK]  
	......
	省略若干行
	......
	[root@centos6 ~]# 

>上面列表表头的内容分别是：镜像的名称，描述，星级，是否为官方镜像，是否是自动构建的
####拉取并查看镜像
	[root@ad2 ~]# docker pull centos
	......
	省略若干行
	......
	[root@ad2 ~]# docker images
	REPOSITORY             TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	docker.io/centos       latest              7322fbe74aa5        7 weeks ago         172.2 MB
	darkhuang/phantom198   latest              3c18eb3b7274        7 weeks ago         643.1 MB
	[root@ad2 ~]#

####查看并导出镜像
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

##docker 容器管理
     docker run --name -h hostname    ##启动容器
     docker stop CONTAINER-ID         ##停止容器
     docker ps                        ##查看活动容器
     docker ps -a                     ##查看所有容器
     docker ps -l                     ##查看最后运行的容器
     docker exec | docker attach      ##进入容器
     docker rm                        ##删除容器

###运行容器执行echo命令，命令执行完成之后，容器会马上退出
	[root@docker-01 ~]# docker run centos /bin/echo "hehe"
	hehe
	[root@docker-01 ~]#
	[root@docker-01 ~]# docker ps  -a
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
	33f548ab24be        centos              "/bin/echo hehe"    3 seconds ago       Exited (0) 2 seconds ago                       desperate_kowalevski
	[root@docker-01 ~]#
###运行容器执行/bin/bash命令，命令执行后，会进入容器的交互界面：
	[root@ad2 ~]# docker run --name mydocker -i -t centos /bin/bash
	[root@bf55a4fd6e73 /]#
	[root@bf55a4fd6e73 /]# ls
	bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
	[root@bf55a4fd6e73 /]# ps  -ef
	UID        PID  PPID  C STIME TTY          TIME CMD
	root         1     0  0 03:10 ?        00:00:00 /bin/bash
	root        19     1  0 03:11 ?        00:00:00 ps -ef
	[root@bf55a4fd6e73 /]#

####注意事项
>
- docker 容器会运行指定的一个应用进程，当应用程序退出之后，容器也就会退出。
- 容器里的进程需要是前台运行的，如果是后台运行的docker会认为容器的任务已经执行完成，就会退出。 
- docker run 命令不同，容器在运行程序后的处理结果不同：
      - 运行echo "hello world",在运行后容器就退出了；
      - 运行/bin/bash后，容器还在运行，不退出。

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

###进入容器 
- 方法1,docker attach 容器I
	- Step1,docker ps           ##查看容器ID
	- Step2,docker attach       ##连接到容器

eg:

	[root@ad2 ~]# docker ps  -l
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
	bf55a4fd6e73        centos:latest       "/bin/bash"         20 minutes ago      Up 15 minutes                           mydocker           
	[root@ad2 ~]# docker attach bf55a4fd6e73
	[root@bf55a4fd6e73 /]#
	[root@bf55a4fd6e73 /]# ls
	bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
	dev  home  lib64  media       opt  root  sbin  sys  usr
	[root@bf55a4fd6e73 /]#

- 方法2,nsenter 容器PID
	- docker inspect --format "{{.State.Pid}}" <容器名称>       ##→获取容器PID
	- nsenter --target 32439 --mount --uts --ipc --net --pid    ##→通过容器PID进入容器





>
nsenter是访问另一个进程的namespace的方式

nsenter的安装方式

	yum  -y install  util-linux

eg:

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


###编写进入容器的小脚本  in.sh
	[root@ad2 scripts]# cat in.sh
	#!/bin/bash
	CNAME=$1
	CPID=$(docker inspect --format "{{.State.Pid}}" $CNAME) 
	nsenter --target "$CPID" --mount --uts --ipc --net --pid 
	[root@ad2 scripts]# chmod +x in.sh
	# 使用in.sh脚本进入指定容器
	[root@ad2 scripts]# ./in.sh myNginx
	root@36e7367300d2:/# exit
	logout
	[root@ad2 scripts]# 
