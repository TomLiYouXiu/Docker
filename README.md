# Docker

Docker学习记录

------

# Docker学习目录

* Docker概述
* Docker安装
* Docker命令
  * 镜像命令
  * 容器命令
  * 操作命令
  * ……
* Docker镜像
* 容器数据卷
* DockerFile
* Docker网络原理
* IDEA整合Docker
* Docker Compose
* Docker Swarm
* CI\CD

~~~
知道的越多，不知道的就越多
~~~

------

# Docker概述

## 概述

开发即运维

环境配置十分麻烦，每个机器都要部署环境

发布项目时，连环境一起发布

Docker解决

![](https://pic.imgdb.cn/item/62a1658509475431292eb55f.jpg)

Docker的思想就来自集装箱

隔离：Docker核心思想！打包装箱！每个箱子是相互隔离的

 Docker通过隔离机制，可以将服务器利用到极致

Docker官方文档地址[Docker Documentation | Docker Documentation](https://docs.docker.com/)

Docker镜像地址[Docker Hub](https://hub.docker.com/)

## Docker功能

1.应用更快速的交付和部署

2.便捷的升级和扩容

3.更简单的系统运维

4.更高效的计算机资源利用

# Docker的安装

## Docker的基本组成

![](https://www.runoob.com/wp-content/uploads/2016/04/576507-docker1.png)

**镜像（Image）：**Docker镜像就好像一个模板，可以通过这个模板来创建容器服务，tomcat

镜像-->run-->tomcat容器（提供服务）

通过这个镜像可以创建多个容器（最终服务运行或者项目的运行就是在容器中）

**容器（container）：**Docker利用容器技术，独立运行一个或一组应用，通过应用来创建的

启动，停止，删除，基本命令

目前可以把这个容器理解为一个简易的linux

**仓库（repository）：**仓库就是存放镜像的地方

仓库分为共有仓库和私有仓库

Docker Hub（默认是国外的）

阿里云……都有容器服务（配置镜像加速）

## 安装Docker

~~~
环境准备
1.需要会一点点linux
2.CentOS
3.shell
~~~

~~~shell
环境查看
#系统内核是3.10以上
[root@iZfdjfsqewlu0jZ ~]# uname -r
3.10.0-514.26.2.el7.x86_64
~~~

~~~
系统配置信息
[root@iZfdjfsqewlu0jZ ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"
CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
~~~

**安装**

查看帮助文档

~~~shell
#1.卸载旧的Docker
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
#2.需要的安装包
yum install -y yum-utils

#3.设置镜像的仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo（国外的）
    
yum-config-manager  \
--add-repo  \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo#推荐阿里云快速

#更新索引
[root@iZfdjfsqewlu0jZ ~]# yum makecache fast

#4.安装Docker相关的源
yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin

#启动docker
systemctl start docker

#查看Docker版本
docker version

Client: Docker Engine - Community
 Version:           20.10.17
 API version:       1.41
 Go version:        go1.17.11
 Git commit:        100c701
 Built:             Mon Jun  6 23:05:12 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.17
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.17.11
  Git commit:       a89b842
  Built:            Mon Jun  6 23:03:33 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.6
  GitCommit:        10c12954828e7c7c9b6e0ea9b0c02b01407d3ae1
 runc:
  Version:          1.1.2
  GitCommit:        v1.1.2-0-ga916309
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

#测试docker用例
docker run hello-world
~~~

![](https://pic.imgdb.cn/item/62a1a72a094754312989deaf.jpg)

~~~shell
#查看一下下载的hello-world镜像
[root@iZfdjfsqewlu0jZ ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   8 months ago   13.3kB

#卸载Docker   
--卸载依赖
yum remove docker-ce docker-ce-cli containerd.io docker-compose-plugin
--删除资源
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
~~~

## 阿里云镜像加速

![](https://pic.imgdb.cn/item/62a1b7b40947543129a1e679.jpg)

配置使用

~~~
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://o6j7yhdy.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
~~~

## 回顾HelloWorld

![](https://pic.imgdb.cn/item/62a1bafd0947543129a66b61.jpg)

## 底层原理

Docker是一个Client-Server结构的系统，Docker的守护进行运行在主机上。通过Socket从客户端进行访问！DockerServer接收到Docker-Client的指令，就会执行这个命令

![](https://pic.imgdb.cn/item/62a1bcdb0947543129a90284.jpg)

Docker为什么比VM快

1.Docker的抽象层少

![](https://pic.imgdb.cn/item/62a1bd670947543129a9bbb4.jpg)

2.Docker利用的是宿主机的内核，VM是新建一个内核

# Docker的常用命令

## 帮助命令

~~~shell
docker version #查看版本
docker info #docker的详细信息
docker 命令--help#帮助命令
~~~

帮助文档地址[docker build | Docker Documentation](https://docs.docker.com/engine/reference/commandline/build/)

## 镜像命令

**docker images**      查看本机的所有镜像

~~~shell
[root@iZfdjfsqewlu0jZ ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   8 months ago   13.3kB

#REPOSITORY 镜像的名字
#TAG 镜像的标签
#IMAGE ID 镜像的ID
#CREATED 镜像的创建时间
#SIZE 镜像的大小

#可选项
  -a, --all             #列出所有镜像
  -q, --quiet           #只显示镜像的ID

~~~

**docker search**  搜索镜像

~~~Shell
[root@iZfdjfsqewlu0jZ ~]# docker search mysql
NAME                           DESCRIPTION                         
            STARS     OFFICIAL   AUTOMATED
mysql                          MySQL is a widely used, open-source 
relation…   12702     [OK]       
mariadb                        MariaDB Server is a high performing 
open sou…   4876      [OK]       

#可选项
  -f, --filter=STARS=3000 #搜索出来的镜像就是START大于3000的
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate output

~~~

**docker pull**   镜像下载

~~~shell
#docker pull 镜像名 [:tag]
[root@iZfdjfsqewlu0jZ ~]# docker pull mysql
Using default tag: latest #如果不写tag就是latest
latest: Pulling from library/mysql
72a69066d2fe: Pull complete #分层下载 ：dockerimages核心 联和文件系统
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
Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  #真实地址

#等价
docker pull mysql
docker pull docker.io/library/mysql:latest

#指定版本下载
docker pull mysql:5.7
[root@iZfdjfsqewlu0jZ ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
72a69066d2fe: Already exists 
93619dbc5b36: Already exists 
99da31dd6142: Already exists 
626033c43d70: Already exists 
37d5d7efb64e: Already exists 
ac563158d721: Already exists 
d2ba16033dad: Already exists 
0ceb82207cd7: Pull complete 
37f2405cae96: Pull complete 
e2482e017e53: Pull complete 
70deed891d42: Pull complete 
Digest: sha256:f2ad209efe9c67104167fc609cca6973c8422939491c9345270175a300419f94
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7

#扩展
  -a, --all-tags                Download all tagged images in the repository
      --disable-content-trust   Skip image verification (default true)
      --platform string         Set platform if server is multi-platform capable
  -q, --quiet                   Suppress verbose output
~~~

**docker rmi**    删除镜像

~~~shell
[root@iZfdjfsqewlu0jZ ~]# docker rmi -f 镜像ID  #删除指定的容器ID
[root@iZfdjfsqewlu0jZ ~]# docker rmi -f 镜像ID1 镜像ID2 镜像ID3 #删除多个容器 
[root@iZfdjfsqewlu0jZ ~]# docker rmi -f $(docker images -aq) #删除全部容器
~~~

## 容器命令

说明：我们有了镜像才可以创建容器，linux，下载一个centos镜像来测试学习

~~~
docker pull centos
~~~

**新建容器并启动**

~~~shell
docker run[可选参数] image

#参数说明
--name="Name"  容器名字 tomcat1 tomcat2 tomcat3 用来区分容器
-d 后台方式运行
-it 使用交互方式运行，进入容器查看内容
-p 指定容器的端口 -p 8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口（常用）
	-p 容器端口
-P 随机指定端口

#测试
#启动并进入容器
[root@iZfdjfsqewlu0jZ ~]# docker run -it centos /bin/bash
[root@45d6d27fa7d6 /]#ls #查看容器内的centos
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
#从容器中退回主机
[root@45d6d27fa7d6 /]# exit
exit
~~~
**查看容器**

~~~shell
#查看当前所有在运行的容器
#docker ps
-a #列出当前正在运行的容器，并带出历史容器
-n=? #显示最近创建的几个容器
-q #只显示容器的编号
[root@iZfdjfsqewlu0jZ ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
#查看以前运行过的容器
[root@iZfdjfsqewlu0jZ ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                          PORTS     NAMES
45d6d27fa7d6   centos         "/bin/bash"   4 minutes ago   Exited (0) About a minute ago             objective_mestorf
aed15223a15c   feb5d9fea6a5   "/hello"      17 hours ago    Exited (0) 17 hours ago                   peaceful_robinson
8fede23a60b2   feb5d9fea6a5   "/hello"      17 hours ago    Exited (0) 17 hours ago                   objective_antonelli
8c399f4637c4   feb5d9fea6a5   "/hello"      17 hours ago    Exited (0) 17 hours ago                   stupefied_germain

~~~

**删除容器**

~~~shell
docker rm 容器ID   #删除指定容器，不能删除正在运行的容器 如果要强制删除 rm -f
docker rm -f $(docker ps -aq) #删除所有容器
docker ps -aq|xargs docker rm #删除所有容器
~~~

**退出容器**

~~~shell
exit #直接退出容器
Ctrl + p + q #容器不停止退出
~~~

**启动和停止容器的操作**

~~~shell
docker start 容器ID #启动容器
docker restart 容器ID #重启容器
docker stop 容器ID #停止当前正在运行的容器
docker kill 容器ID #强制停止当前的容器
~~~

## 常用的其他命令

**后台启动**

~~~shell
[root@iZfdjfsqewlu0jZ ~]# docker run -d centos
aa22af65cfa5df9e9061901ae17a8894519db2715c57e1f2f18c717ac1418914
#问题docker ps发现centos停止了

#常见的问题 docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自己停止，
~~~

**查看日志**

~~~shell
docker logs -tf --tail 条数 容器ID

#自己编写一段shell脚本
[root@iZfdjfsqewlu0jZ ~]# docker run -d centos /bin/sh -c "while true;do echo liyouxiu;sleep 1;done"
8b27a4c617190f9607eeee54c5ee7287418de4032ffab59617bdeb07f67fd051

#查看日志
[root@iZfdjfsqewlu0jZ ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
8b27a4c61719   centos    "/bin/sh -c 'while t…"   54 seconds ago   Up 54 seconds             gracious_kare

[root@iZfdjfsqewlu0jZ ~]# docker logs -tf --tail 10 8b27a4c61719
2022-06-12T12:05:11.110496256Z liyouxiu
2022-06-12T12:05:12.113114538Z liyouxiu
2022-06-12T12:05:13.115873163Z liyouxiu
2022-06-12T12:05:14.118445564Z liyouxiu
2022-06-12T12:05:15.120843830Z liyouxiu
2022-06-12T12:05:16.123262855Z liyouxiu
2022-06-12T12:05:17.126339630Z liyouxiu
2022-06-12T12:05:18.128521488Z liyouxiu
2022-06-12T12:05:19.131056900Z liyouxiu
2022-06-12T12:05:20.133498323Z liyouxiu
2022-06-12T12:05:21.136000568Z liyouxiu
#显示日志
-tf   #显示日志
--tail number #显示日志的条数

~~~

**查看容器中进程信息**

~~~shell
#docker top 容器ID
[root@iZfdjfsqewlu0jZ ~]# docker top d24668257c72
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                5327                5277                0                   20:09               pts/0               00:00:00            /bin/bash

~~~

**查看镜像元数据**

~~~shell
#docker inspect 容器ID
[root@iZfdjfsqewlu0jZ ~]# docker inspect d24668257c72
[
    {
        "Id": "d24668257c7242f9c237f3ae431e83cd2c0bbe9f1936d8aaa130a1a11d4f660a",
        "Created": "2022-06-12T12:09:38.343790094Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 5327,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-06-12T12:09:38.728481556Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/d24668257c7242f9c237f3ae431e83cd2c0bbe9f1936d8aaa130a1a11d4f660a/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/d24668257c7242f9c237f3ae431e83cd2c0bbe9f1936d8aaa130a1a11d4f660a/hostname",
        "HostsPath": "/var/lib/docker/containers/d24668257c7242f9c237f3ae431e83cd2c0bbe9f1936d8aaa130a1a11d4f660a/hosts",
        "LogPath": "/var/lib/docker/containers/d24668257c7242f9c237f3ae431e83cd2c0bbe9f1936d8aaa130a1a11d4f660a/d24668257c7242f9c237f3ae431e83cd2c0bbe9f1936d8aaa130a1a11d4f660a-json.log",
        "Name": "/suspicious_hodgkin",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/51bf2fa7c66e7719010763c377dacc5adcc0679d0b286a752f54346892fc8ac5-init/diff:/var/lib/docker/overlay2/1844e91d48086f6ee51464363981949796755ef3647d489a4b68c69fce9597c6/diff",
                "MergedDir": "/var/lib/docker/overlay2/51bf2fa7c66e7719010763c377dacc5adcc0679d0b286a752f54346892fc8ac5/merged",
                "UpperDir": "/var/lib/docker/overlay2/51bf2fa7c66e7719010763c377dacc5adcc0679d0b286a752f54346892fc8ac5/diff",
                "WorkDir": "/var/lib/docker/overlay2/51bf2fa7c66e7719010763c377dacc5adcc0679d0b286a752f54346892fc8ac5/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "d24668257c72",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "e7d1ec9b04028c9518c766a6f8f844a9f1410d15e78f832b3dfd989b3a7b0cf6",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/e7d1ec9b0402",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "9fdf53a8bec5c7f477295dfbc71acdbb389a58ea7edc88c1277153a6da71ccf3",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "c1c3f09d7c5e2e5638d651a296ec87b5ec9db0fe613083ccf02a22529cdeea7b",
                    "EndpointID": "9fdf53a8bec5c7f477295dfbc71acdbb389a58ea7edc88c1277153a6da71ccf3",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

~~~

**进入当前正在运行的容器**

~~~shell
#我们通常容器使用的都是后台方式运行的，需要进入容器，修改配置

#命令
docker exec -it 容器ID /bin/bash

#测试
[root@iZfdjfsqewlu0jZ /]# docker exec -it d24668257c72 /bin/bash
[root@d24668257c72 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 12:09 pts/0    00:00:00 /bin/bash
root        21     0  0 12:22 pts/1    00:00:00 /bin/bash
root        36    21  0 12:23 pts/1    00:00:00 ps -ef

#方式二
docker attach 容器ID 
#测试
[root@iZfdjfsqewlu0jZ /]# docker attach d24668257c72
[root@d24668257c72 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 12:09 pts/0    00:00:00 /bin/bash
root        37     1  0 12:25 pts/0    00:00:00 ps -ef


#docker exec   进入容器之后相当于开启了一个新的终端  
#docker attach 进入容器正在运行的中断，不会开启新的终端

~~~

**从容器内拷贝文件到主机上**

~~~shell
docker cp 容器ID：容器内路径 目的的主机路径

#进入容器
[root@iZfdjfsqewlu0jZ home]# docker attach d24668257c72
#创建文件
[root@d24668257c72 /]# cd home/
[root@d24668257c72 home]# touch liyouxiu.txt
[root@d24668257c72 home]# ls
liyouxiu.txt
[root@d24668257c72 home]# exit
exit
#拷贝容器的文件到主机内
[root@iZfdjfsqewlu0jZ home]# docker cp d24668257c72:/home/liyouxiu.txt /home
[root@iZfdjfsqewlu0jZ home]# ls
admin  liyouxiu.java  liyouxiu.txt

#拷贝是一个手动的过程，未来我们使用-v 卷的技术可以实现数据的同步
~~~

