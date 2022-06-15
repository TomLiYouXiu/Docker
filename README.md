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

![](https://img-blog.csdnimg.cn/20210717134852290.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1YW5namhhaQ==,size_16,color_FFFFFF,t_70)

# 作业练习

## 作业一

Docker安装Nginx

~~~shell
#1.搜索镜像（最好去Docker hub上查看详细信息）
[root@iZfdjfsqewlu0jZ ~]# docker search nginx
NAME                                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
nginx                                             Official build of Nginx.                        16944     [OK]       
linuxserver/nginx                                 An Nginx container, brought to you by LinuxS…   169                  
bitnami/nginx                                     Bitnami nginx Docker Image                      131                  [OK]

#2.下载镜像
[root@iZfdjfsqewlu0jZ ~]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
a2abf6c4d29d: Pull complete 
a9edb18cadd1: Pull complete 
589b7251471a: Pull complete 
186b1aaa4aa6: Pull complete 
b4df32aa5a72: Pull complete 
a0bcbecc962e: Pull complete 
Digest: sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest

#3.镜像查看
[root@iZfdjfsqewlu0jZ ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
nginx         latest    605c77e624dd   5 months ago   141MB
mysql         5.7       c20987f18b13   5 months ago   448MB
mysql         latest    3218b38490ce   5 months ago   516MB
hello-world   latest    feb5d9fea6a5   8 months ago   13.3kB
centos        latest    5d0da3dc9764   9 months ago   231MB

#4.启动
[root@iZfdjfsqewlu0jZ ~]# docker run -d --name nginx01 -p 33344:80 nginx
#docker run -d --name nginx01 -p 33344:80 nginx -d 后台启动  -p 33344:80 物理机端口映射：docker容器端口
2b387c31a865d974817f2072b8cf0fc3c66199b38d9af70bdbd1d9a50203347d
[root@iZfdjfsqewlu0jZ ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                     NAMES
2b387c31a865   nginx     "/docker-entrypoint.…"   8 seconds ago   Up 7 seconds   0.0.0.0:33344->80/tcp, :::33344->80/tcp   nginx01


#5.测试连接
[root@iZfdjfsqewlu0jZ ~]# curl localhost:33344
#curl linux测试网页链接
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

#6.进入Nginx
[root@iZfdjfsqewlu0jZ ~]# docker exec -it nginx01 /bin/bash
root@2b387c31a865:/# whereis nginx 
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@2b387c31a865:/# cd /etc/nginx/
root@2b387c31a865:/etc/nginx# ls
conf.d  fastcgi_params  mime.types  modules  nginx.conf  scgi_params  uwsgi_params
root@2b387c31a865:/etc/nginx# 

~~~

## 作业二

使用docker安装tomcat

**方法一**

~~~shell
#1.搜索镜像
[root@iZfdjfsqewlu0jZ ~]# docker search tomcat
NAME                                           DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
tomcat                                         Apache Tomcat is an open source implementati…   3342      [OK]       
tomee                                          Apache TomEE is an all-Apache Java EE certif…   97        [OK]       

#2.下载镜像
[root@iZfdjfsqewlu0jZ ~]# docker pull tomcat
Using default tag: latest
latest: Pulling from library/tomcat
0e29546d541c: Pull complete 
9b829c73b52b: Pull complete 
cb5b7ae36172: Pull complete 
6494e4811622: Pull complete 
668f6fcc5fa5: Pull complete 
dc120c3e0290: Pull complete 
8f7c0eebb7b1: Pull complete 
77b694f83996: Pull complete 
0f611256ec3a: Pull complete 
4f25def12f23: Pull complete 
Digest: sha256:9dee185c3b161cdfede1f5e35e8b56ebc9de88ed3a79526939701f3537a52324
Status: Downloaded newer image for tomcat:latest
docker.io/library/tomcat:latest

#3.查看镜像
[root@iZfdjfsqewlu0jZ ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
nginx         latest    605c77e624dd   5 months ago   141MB
tomcat        latest    fb5657adc892   5 months ago   680MB
mysql         5.7       c20987f18b13   5 months ago   448MB
mysql         latest    3218b38490ce   5 months ago   516MB
hello-world   latest    feb5d9fea6a5   8 months ago   13.3kB
centos        latest    5d0da3dc9764   9 months ago   231MB

#4.后台启动
[root@iZfdjfsqewlu0jZ ~]# docker run -d --name tomcat00 -p 8080:8080 tomcat
ba614f419da44865d644685a694fcc8eb6dc24634ca6542ff814607c670be0b8
[root@iZfdjfsqewlu0jZ ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                       NAMES
ba614f419da4   tomcat    "catalina.sh run"        36 seconds ago   Up 35 seconds   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   tomcat00
2b387c31a865   nginx     "/docker-entrypoint.…"   24 hours ago     Up 24 hours     0.0.0.0:33344->80/tcp, :::33344->80/tcp     nginx01

#5.测试连接
[root@iZfdjfsqewlu0jZ ~]# curl localhost:8080
<!doctype html><html lang="en"><head><title>HTTP Status 404 – Not Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 – Not Found</h1><hr class="line" /><p><b>Type</b> Status Report</p><p><b>Description</b> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.</p><hr class="line" /><h3>Apache Tomcat/10.0.14</h3></body></html>

#6.进入
[root@iZfdjfsqewlu0jZ ~]# docker exec -it tomcat00 /bin/bash
root@ba614f419da4:/usr/local/tomcat# 
root@ba614f419da4:/usr/local/tomcat# ls
BUILDING.txt     LICENSE  README.md      RUNNING.txt  conf  logs            temp     webapps.dist
CONTRIBUTING.md  NOTICE   RELEASE-NOTES  bin          lib   native-jni-lib  webapps  work

#发现问题
#linux命令少了，webapps文件夹为空
#镜像的原因，默认是最小的镜像，所有不必要的都删除掉
#保证最小的可运行环境

#文件都在webapps.dist
root@50e480f5f0e6:/usr/local/tomcat# cd webapps.dist/
root@50e480f5f0e6:/usr/local/tomcat/webapps.dist# ll
bash: ll: command not found
root@50e480f5f0e6:/usr/local/tomcat/webapps.dist# ls
ROOT  docs  examples  host-manager  manager
#复制
root@50e480f5f0e6:/usr/local/tomcat# cp -r webapps.dist/* webapps/
root@50e480f5f0e6:/usr/local/tomcat# cd webapps
root@50e480f5f0e6:/usr/local/tomcat/webapps# ls
ROOT  docs  examples  host-manager  manager

#查看
[root@iZfdjfsqewlu0jZ ~]# curl 127.0.0.1:8080



<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <title>Apache Tomcat/10.0.14</title>
        <link href="favicon.ico" rel="icon" type="image/x-icon" />
        <link href="tomcat.css" rel="stylesheet" type="text/css" />
    </head>

    <body>
        <div id="wrapper">
            <div id="navigation" class="curved container">
                <span id="nav-home"><a href="https://tomcat.apache.org/">Home</a></span>
                <span id="nav-hosts"><a href="/docs/">Documentation</a></span>
                <span id="nav-config"><a href="/docs/config/">Configuration</a></span>
                <span id="nav-examples"><a href="/examples/">Examples</a></span>
                <span id="nav-wiki"><a href="https://wiki.apache.org/tomcat/FrontPage">Wiki</a></span>
                <span id="nav-lists"><a href="https://tomcat.apache.org/lists.html">Mailing Lists</a></span>
                <span id="nav-help"><a href="https://tomcat.apache.org/findhelp.html">Find Help</a></span>
                <br class="separator" />
            </div>
            <div id="asf-box">
                <h1>Apache Tomcat/10.0.14</h1>
            </div>
            <div id="upper" class="curved container">
                <div id="congrats" class="curved container">
                    <h2>If you're seeing this, you've successfully installed Tomcat. Congratulations!</h2>
                </div>
                <div id="notice">
                    <img id="tomcat-logo" src="tomcat.svg" alt="[tomcat logo]" />
                    <div id="tasks">
                        <h3>Recommended Reading:</h3>
                        <h4><a href="/docs/security-howto.html">Security Considerations How-To</a></h4>
                        <h4><a href="/docs/manager-howto.html">Manager Application How-To</a></h4>
                        <h4><a href="/docs/cluster-howto.html">Clustering/Session Replication How-To</a></h4>
                    </div>
                </div>
                <div id="actions">
                    <div class="button">
                        <a class="container shadow" href="/manager/status"><span>Server Status</span></a>
                    </div>
                    <div class="button">
                        <a class="container shadow" href="/manager/html"><span>Manager App</span></a>
                    </div>
                    <div class="button">
                        <a class="container shadow" href="/host-manager/html"><span>Host Manager</span></a>
                    </div>
                </div>
                <br class="separator" />
            </div>
            <div id="middle" class="curved container">
                <h3>Developer Quick Start</h3>
                <div class="col25">
                    <div class="container">
                        <p><a href="/docs/setup.html">Tomcat Setup</a></p>
                        <p><a href="/docs/appdev/">First Web Application</a></p>
                    </div>
                </div>
                <div class="col25">
                    <div class="container">
                        <p><a href="/docs/realm-howto.html">Realms &amp; AAA</a></p>
                        <p><a href="/docs/jndi-datasource-examples-howto.html">JDBC DataSources</a></p>
                    </div>
                </div>
                <div class="col25">
                    <div class="container">
                        <p><a href="/examples/">Examples</a></p>
                    </div>
                </div>
                <div class="col25">
                    <div class="container">
                        <p><a href="https://wiki.apache.org/tomcat/Specifications">Servlet Specifications</a></p>
                        <p><a href="https://wiki.apache.org/tomcat/TomcatVersions">Tomcat Versions</a></p>
                    </div>
                </div>
                <br class="separator" />
            </div>
            <div id="lower">
                <div id="low-manage" class="">
                    <div class="curved container">
                        <h3>Managing Tomcat</h3>
                        <p>For security, access to the <a href="/manager/html">manager webapp</a> is restricted.
                        Users are defined in:</p>
                        <pre>$CATALINA_HOME/conf/tomcat-users.xml</pre>
                        <p>In Tomcat 10.0 access to the manager application is split between
                           different users. &nbsp; <a href="/docs/manager-howto.html">Read more...</a></p>
                        <br />
                        <h4><a href="/docs/RELEASE-NOTES.txt">Release Notes</a></h4>
                        <h4><a href="/docs/changelog.html">Changelog</a></h4>
                        <h4><a href="https://tomcat.apache.org/migration.html">Migration Guide</a></h4>
                        <h4><a href="https://tomcat.apache.org/security.html">Security Notices</a></h4>
                    </div>
                </div>
                <div id="low-docs" class="">
                    <div class="curved container">
                        <h3>Documentation</h3>
                        <h4><a href="/docs/">Tomcat 10.0 Documentation</a></h4>
                        <h4><a href="/docs/config/">Tomcat 10.0 Configuration</a></h4>
                        <h4><a href="https://wiki.apache.org/tomcat/FrontPage">Tomcat Wiki</a></h4>
                        <p>Find additional important configuration information in:</p>
                        <pre>$CATALINA_HOME/RUNNING.txt</pre>
                        <p>Developers may be interested in:</p>
                        <ul>
                            <li><a href="https://tomcat.apache.org/bugreport.html">Tomcat 10.0 Bug Database</a></li>
                            <li><a href="/docs/api/index.html">Tomcat 10.0 JavaDocs</a></li>
                            <li><a href="https://github.com/apache/tomcat/tree/10.0.x">Tomcat 10.0 Git Repository at GitHub</a></li>
                        </ul>
                    </div>
                </div>
                <div id="low-help" class="">
                    <div class="curved container">
                        <h3>Getting Help</h3>
                        <h4><a href="https://tomcat.apache.org/faq/">FAQ</a> and <a href="https://tomcat.apache.org/lists.html">Mailing Lists</a></h4>
                        <p>The following mailing lists are available:</p>
                        <ul>
                            <li id="list-announce"><strong><a href="https://tomcat.apache.org/lists.html#tomcat-announce">tomcat-announce</a><br />
                                Important announcements, releases, security vulnerability notifications. (Low volume).</strong>
                            </li>
                            <li><a href="https://tomcat.apache.org/lists.html#tomcat-users">tomcat-users</a><br />
                                User support and discussion
                            </li>
                            <li><a href="https://tomcat.apache.org/lists.html#taglibs-user">taglibs-user</a><br />
                                User support and discussion for <a href="https://tomcat.apache.org/taglibs/">Apache Taglibs</a>
                            </li>
                            <li><a href="https://tomcat.apache.org/lists.html#tomcat-dev">tomcat-dev</a><br />
                                Development mailing list, including commit messages
                            </li>
                        </ul>
                    </div>
                </div>
                <br class="separator" />
            </div>
            <div id="footer" class="curved container">
                <div class="col20">
                    <div class="container">
                        <h4>Other Downloads</h4>
                        <ul>
                            <li><a href="https://tomcat.apache.org/download-connectors.cgi">Tomcat Connectors</a></li>
                            <li><a href="https://tomcat.apache.org/download-native.cgi">Tomcat Native</a></li>
                            <li><a href="https://tomcat.apache.org/taglibs/">Taglibs</a></li>
                            <li><a href="/docs/deployer-howto.html">Deployer</a></li>
                        </ul>
                    </div>
                </div>
                <div class="col20">
                    <div class="container">
                        <h4>Other Documentation</h4>
                        <ul>
                            <li><a href="https://tomcat.apache.org/connectors-doc/">Tomcat Connectors</a></li>
                            <li><a href="https://tomcat.apache.org/connectors-doc/">mod_jk Documentation</a></li>
                            <li><a href="https://tomcat.apache.org/native-doc/">Tomcat Native</a></li>
                            <li><a href="/docs/deployer-howto.html">Deployer</a></li>
                        </ul>
                    </div>
                </div>
                <div class="col20">
                    <div class="container">
                        <h4>Get Involved</h4>
                        <ul>
                            <li><a href="https://tomcat.apache.org/getinvolved.html">Overview</a></li>
                            <li><a href="https://tomcat.apache.org/source.html">Source Repositories</a></li>
                            <li><a href="https://tomcat.apache.org/lists.html">Mailing Lists</a></li>
                            <li><a href="https://wiki.apache.org/tomcat/FrontPage">Wiki</a></li>
                        </ul>
                    </div>
                </div>
                <div class="col20">
                    <div class="container">
                        <h4>Miscellaneous</h4>
                        <ul>
                            <li><a href="https://tomcat.apache.org/contact.html">Contact</a></li>
                            <li><a href="https://tomcat.apache.org/legal.html">Legal</a></li>
                            <li><a href="https://www.apache.org/foundation/sponsorship.html">Sponsorship</a></li>
                            <li><a href="https://www.apache.org/foundation/thanks.html">Thanks</a></li>
                        </ul>
                    </div>
                </div>
                <div class="col20">
                    <div class="container">
                        <h4>Apache Software Foundation</h4>
                        <ul>
                            <li><a href="https://tomcat.apache.org/whoweare.html">Who We Are</a></li>
                            <li><a href="https://tomcat.apache.org/heritage.html">Heritage</a></li>
                            <li><a href="https://www.apache.org">Apache Home</a></li>
                            <li><a href="https://tomcat.apache.org/resources.html">Resources</a></li>
                        </ul>
                    </div>
                </div>
                <br class="separator" />
            </div>
            <p class="copyright">Copyright &copy;1999-2022 Apache Software Foundation.  All Rights Reserved</p>
        </div>
    </body>

</html>

~~~

**方法二**

~~~SHELL
docker run -it --rm tomcat:9.0

#我们之前的启动都是后台的，停止了容器之后，容器还是可以查到的 docker run -it --rm 一般用来测试，用完就删除
[root@iZfdjfsqewlu0jZ ~]# docker run -it --rm tomcat:9.0
Unable to find image 'tomcat:9.0' locally
9.0: Pulling from library/tomcat
0e29546d541c: Already exists 
9b829c73b52b: Already exists 
cb5b7ae36172: Already exists 
6494e4811622: Already exists 
668f6fcc5fa5: Already exists 
dc120c3e0290: Already exists 
8f7c0eebb7b1: Already exists 
77b694f83996: Already exists 
7662046c36cb: Pull complete 
b93639122cb4: Pull complete 
Digest: sha256:cd96d4f7d3f5fc4d3bc1622ec678207087b8215d55021a607ecaefba80b403ea
Status: Downloaded newer image for tomcat:9.0
Using CATALINA_BASE:   /usr/local/tomcat
Using CATALINA_HOME:   /usr/local/tomcat
Using CATALINA_TMPDIR: /usr/local/tomcat/temp
Using JRE_HOME:        /usr/local/openjdk-11
Using CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
Using CATALINA_OPTS:   
NOTE: Picked up JDK_JAVA_OPTIONS:  --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.rmi/sun.rmi.transport=ALL-UNNAMED
15-Jun-2022 07:49:47.047 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Server version name:   Apache Tomcat/9.0.56
15-Jun-2022 07:49:47.086 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Server built:          Dec 2 2021 14:30:07 UTC
15-Jun-2022 07:49:47.086 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Server version number: 9.0.56.0
15-Jun-2022 07:49:47.086 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log OS Name:               Linux
15-Jun-2022 07:49:47.086 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log OS Version:            4.18.0-193.14.2.el8_2.x86_64
15-Jun-2022 07:49:47.086 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Architecture:          amd64
15-Jun-2022 07:49:47.087 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Java Home:             /usr/local/openjdk-11
15-Jun-2022 07:49:47.087 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log JVM Version:           11.0.13+8
15-Jun-2022 07:49:47.087 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log JVM Vendor:            Oracle Corporation
15-Jun-2022 07:49:47.087 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log CATALINA_BASE:         /usr/local/tomcat
15-Jun-2022 07:49:47.087 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log CATALINA_HOME:         /usr/local/tomcat
15-Jun-2022 07:49:47.158 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: --add-opens=java.base/java.lang=ALL-UNNAMED
15-Jun-2022 07:49:47.158 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: --add-opens=java.base/java.io=ALL-UNNAMED
15-Jun-2022 07:49:47.158 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: --add-opens=java.base/java.util=ALL-UNNAMED
15-Jun-2022 07:49:47.158 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: --add-opens=java.base/java.util.concurrent=ALL-UNNAMED
15-Jun-2022 07:49:47.159 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: --add-opens=java.rmi/sun.rmi.transport=ALL-UNNAMED
15-Jun-2022 07:49:47.159 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: -Djava.util.logging.config.file=/usr/local/tomcat/conf/logging.properties
15-Jun-2022 07:49:47.159 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager
15-Jun-2022 07:49:47.160 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: -Djdk.tls.ephemeralDHKeySize=2048
15-Jun-2022 07:49:47.162 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: -Djava.protocol.handler.pkgs=org.apache.catalina.webresources
15-Jun-2022 07:49:47.162 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: -Dorg.apache.catalina.security.SecurityListener.UMASK=0027
15-Jun-2022 07:49:47.162 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: -Dignore.endorsed.dirs=
15-Jun-2022 07:49:47.162 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: -Dcatalina.base=/usr/local/tomcat
15-Jun-2022 07:49:47.162 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: -Dcatalina.home=/usr/local/tomcat
15-Jun-2022 07:49:47.162 INFO [main] org.apache.catalina.startup.VersionLoggerListener.log Command line argument: -Djava.io.tmpdir=/usr/local/tomcat/temp
15-Jun-2022 07:49:47.171 INFO [main] org.apache.catalina.core.AprLifecycleListener.lifecycleEvent Loaded Apache Tomcat Native library [1.2.31] using APR version [1.7.0].
15-Jun-2022 07:49:47.171 INFO [main] org.apache.catalina.core.AprLifecycleListener.lifecycleEvent APR capabilities: IPv6 [true], sendfile [true], accept filters [false], random [true], UDS [true].
15-Jun-2022 07:49:47.171 INFO [main] org.apache.catalina.core.AprLifecycleListener.lifecycleEvent APR/OpenSSL configuration: useAprConnector [false], useOpenSSL [true]
15-Jun-2022 07:49:47.177 INFO [main] org.apache.catalina.core.AprLifecycleListener.initializeSSL OpenSSL successfully initialized [OpenSSL 1.1.1k  25 Mar 2021]
15-Jun-2022 07:49:48.157 INFO [main] org.apache.coyote.AbstractProtocol.init Initializing ProtocolHandler ["http-nio-8080"]
15-Jun-2022 07:49:48.253 INFO [main] org.apache.catalina.startup.Catalina.load Server initialization in [1787] milliseconds
15-Jun-2022 07:49:48.439 INFO [main] org.apache.catalina.core.StandardService.startInternal Starting service [Catalina]
15-Jun-2022 07:49:48.440 INFO [main] org.apache.catalina.core.StandardEngine.startInternal Starting Servlet engine: [Apache Tomcat/9.0.56]
15-Jun-2022 07:49:48.468 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["http-nio-8080"]
15-Jun-2022 07:49:48.499 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [244] milliseconds

#接着执行方式一
~~~

## 作业三

部署ES+kibana