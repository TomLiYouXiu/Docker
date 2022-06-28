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

~~~
es暴露的端口很多
es十分的耗内存
es的数据一般需要存放到安全目录
~~~

~~~shell
#启动elasticsearch
$ docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag

#启动之后非常的卡
#查看cpu的状态 docker stats
#测试es
~~~

感觉没什么必要这个作业所以没有继续往下做

# 可视化

* portainer(可以先用着个)

**什么是portainer？**

Docker图形化界面管理工具！提供后台面板供我们操作

~~~shell
docker run -p 9000:9000 -p 8000:8000 --name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /mydata/portainer/data:/data \
-d portainer/portainer
~~~

~~~shell
#测试连接
http://10*********4:9000/
#第一次需要创建账号

~~~

**借鉴知乎大佬[(46 封私信 / 80 条消息) macrozheng - 知乎 (zhihu.com)](https://www.zhihu.com/people/macrozheng)**

- 第一次登录的时候需要创建管理员账号，访问地址：http://10*********4:9000/

![img](https://pic4.zhimg.com/80/v2-7c8e004682cf1f021dbd0d1a590d73cb_1440w.jpg)



- 之后我们选择连接到本地的Docker环境，连接完成后我们就可以愉快地使用Portainer进行可视化管理了！



![img](https://pic4.zhimg.com/80/v2-aa3d2a40980cbab83e4941e48d033fb7_1440w.jpg)



## 使用

- 登录成功后，可以发现有一个本地的Docker环境；



![img](https://pic4.zhimg.com/80/v2-c351496a453b108fd57b5143412b50db_1440w.jpg)



- 打开Dashboard菜单可以看到Docker环境的概览信息，比如运行了几个容器，有多少个镜像等；



![img](https://pic3.zhimg.com/80/v2-e530b5970b4c00486ed76c51e8bfa07a_1440w.jpg)



- 打开App Templates菜单可以看到很多创建容器的模板，通过模板设置下即可轻松创建容器，支持的应用还是挺多的；



![img](https://pic1.zhimg.com/80/v2-a65eb19fa8a460e24fd92ffd40df754c_1440w.jpg)



- 打开Containers菜单，可以看到当前创建的容器，我们可以对容器进行运行、暂停、删除等操作；



![img](https://pic4.zhimg.com/80/v2-0521d7d66abc70a04ef746406d5cb327_1440w.jpg)



- 选择一个容器，点击Logs按钮，可以直接查看容器运行日志，可以和`docker logs`命令说再见了；



![img](https://pic3.zhimg.com/80/v2-0f7c94e1066de5c05349649fa105dafe_1440w.jpg)



- 点击Inspect按钮，可以查看容器信息，比如看看容器运行的IP地址；



![img](https://pic4.zhimg.com/80/v2-947a0ba0ff2112faf1df54ea10da3487_1440w.jpg)



- 点击Stats按钮，可以查看容器的内存、CPU及网络的使用情况，性能分析不愁了；



![img](https://pic3.zhimg.com/80/v2-94cf19dd9e486e80fc372f73d0702532_1440w.jpg)



- 点击Console按钮，可以进入到容器中去执行命令，比如我们可以进入到MySQL容器中去执行登录命令；



![img](https://pic1.zhimg.com/80/v2-a98e25941d40aa75593f55112b5a068c_1440w.jpg)



- 打开Images菜单，我们可以查看所有的本地镜像，对镜像进行管理；



![img](https://pic2.zhimg.com/80/v2-bd8855504fdb6e4e012b19bafcc050c9_1440w.jpg)



- 打开Networks菜单，可以查看Docker环境中的网络情况；



![img](https://pic4.zhimg.com/80/v2-55261dec5f9888990938b0fd24b78623_1440w.jpg)



- 打开Users菜单，我们可以创建Portainer的用户，并给他们赋予相应的角色；



![img](https://pic2.zhimg.com/80/v2-30f05a2c2c97e6a00dcf210dcfa9b9e5_1440w.jpg)



- 打开Registries菜单，我们可以配置自己的镜像仓库，这样在拉取镜像的时候，就可以选择从自己的镜像仓库拉取了。



![img](https://pic1.zhimg.com/80/v2-b6ef10c2dd12e3ff2e6234854a32fd9c_1440w.jpg)

* Rancher（CI/CD）

~~~shell

~~~

# Docker镜像

## 镜像是什么

镜像是一种轻量级的，可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需要的的所有内容，包括代码，运行时 ，库，环境变量和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来

如何得到镜像：

* 远程仓库下载
* 自己制作DockerFile

## Docker镜像加载原理

**联合文件系统：UnionFS**
联合文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。
联合文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

![](https://pic.imgdb.cn/item/62ac706009475431297faa4a.jpg)

特性：
一次同时加载多个文件系统，但从外面看来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

docker镜像结构详细描述
Linux 文件系统结构：

典型的 Linux 文件系统由 bootfs 和 rootfs 两部分组成。bootfs(boot file system) 主要包含 bootloader 和 kernel，bootloader 主要是引导加载 kernel(内核)，当 kernel 被加载到内存中后 bootfs 就被 umount (卸载)了。

rootfs (root file system) 包含的就是典型 Linux 系统中的 /dev，/proc，/bin，/etc 等标准目录和文件。rootfs就是各种Linux发行版。比如redcat、centOS。

docker镜像结构：

Docker 容器是建立在 Aufs 基础上的，Aufs（以前称之为Another Union FS，后来绝不不够高大上，更名为Advanced Union FS）是一种Union FS， 简单来说就是支持将不同的目录挂载到同一个虚拟文件系统下，并实现一种 layer 的概念。Aufs 将挂载到同一虚拟文件系统下的多个目录分别设置成 read-only，read-write 以及 whiteout-able 权限，对 read-only 目录只能读，而写操作只能实施在 read-write 目录中。重点在于，写操作是在 read-only 上的一种增量操作，不影响 read-only 目录。当挂载目录的时候要严格按照各目录之间的这种增量关系，将被增量操作的目录优先于在它基础上增量操作的目录挂载，待所有目录挂载结束了，继续挂载一个 read-write 目录，如此便形成了一种层次结构。

传统的 Linux 加载 bootfs 时会先将 rootfs 设为 read-only，然后在系统自检之后将 rootfs 从 read-only 改为 read-write，然后我们就可以在 rootfs 上进行写和读的操作了。但 Docker 的镜像却不是这样，它在 bootfs 自检完毕之后并不会把 rootfs 的 read-only 改为 read-write。而是利用 union mount（UnionFS 的一种挂载机制）将一个或多个 read-only 的 rootfs 加载到之前的 read-only 的 rootfs 层之上。在加载了这么多层的 rootfs 之后，仍然让它看起来只像是一个文件系统，在 Docker 的体系里把 union mount 的这些 read-only 的 rootfs 叫做 Docker 的镜像。但是，此时的每一层 rootfs 都是 read-only 的，我们此时还不能对其进行操作。当我们创建一个容器，也就是将 Docker 镜像进行实例化，系统会在一层或是多层 read-only 的 rootfs 之上分配一层空的 read-write 的 rootfs。

总结：

对于一个精简的OS，rootfs可以很小，只需要包含基本的命令，工具和程序库就可以，因为底层直接用host的kernel(主机的内核)，自己只需要提供rootfs即可。
由此可见，Linux的不同发行版本，bootfs基本是一致的，rootfs会有差别，因此不同发行版本可以共用bootfs。
bootfs和内核的连接启动是很慢的，这也解释了，为什么虚拟机启动是分钟级别(就像启动电脑要开机，开机很慢)，而镜像启动是秒级的(电脑已经开机，打开软件就可以)。

~~~shell
#查看镜像分层具体信息

[root@iZfdjfsqewlu0jZ /]# docker image inspect redis
[
    {
        "Id": "sha256:7614ae9453d1d87e740a2056257a6de7135c84037c367e1fffa92ae922784631",
        "RepoTags": [
            "redis:latest"
        ],
        "RepoDigests": [
            "redis@sha256:db485f2e245b5b3329fdc7eff4eb00f913e09d8feb9ca720788059fdc2ed8339"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2021-12-21T12:42:49.755107412Z",
        "Container": "13d25f53410417c5220c8dfe8bd49f06abdbcd69faa62a9b877de02464bb04a3",
        "ContainerConfig": {
            "Hostname": "13d25f534104",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "Image": "sha256:e093f59d716c95cfce82c676f099b960cc700432ab531388fcedf79932fc81ec",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "20.10.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "redis-server"
            ],
            "Image": "sha256:e093f59d716c95cfce82c676f099b960cc700432ab531388fcedf79932fc81ec",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 112691373,
        "VirtualSize": 112691373,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/b58d95856eeb6feed789f32d324856818892daaa701e2eb778220b7ca9e26634/diff:/var/lib/docker/overlay2/c141a1f7109f2356d4948ef6d9c6efdc9ffcc6697638ae15260dfdd83ad3827c/diff:/var/lib/docker/overlay2/b70a2a307714a1ce18b2486f4d5bb4a66fafe4127841327dabe549ec94f32ce6/diff:/var/lib/docker/overlay2/9f3dda2349a92df055f4208385ce4d24226427663b142b932787757a88f32de2/diff:/var/lib/docker/overlay2/ea4e79ae9d30212a97222a053ac1d71f34ba6089cc27d54cf9c53da4e06024c9/diff",
                "MergedDir": "/var/lib/docker/overlay2/231ac64eaf234f180656e26a2e464a27ca72b79bcc7c5726a1138c7e3d107431/merged",
                "UpperDir": "/var/lib/docker/overlay2/231ac64eaf234f180656e26a2e464a27ca72b79bcc7c5726a1138c7e3d107431/diff",
                "WorkDir": "/var/lib/docker/overlay2/231ac64eaf234f180656e26a2e464a27ca72b79bcc7c5726a1138c7e3d107431/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:2edcec3590a4ec7f40cf0743c15d78fb39d8326bc029073b41ef9727da6c851f",
                "sha256:9b24afeb7c2f21e50a686ead025823cd2c6e9730c013ca77ad5f115c079b57cb",
                "sha256:4b8e2801e0f956a4220c32e2c8b0a590e6f9bd2420ec65453685246b82766ea1",
                "sha256:529cdb636f61e95ab91a62a51526a84fd7314d6aab0d414040796150b4522372",
                "sha256:9975392591f2777d6bf4d9919ad1b2c9afa12f9a9b4d260f45025ec3cc9b18ed",
                "sha256:8e5669d8329116b8444b9bbb1663dda568ede12d3dbcce950199b582f6e94952"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]

~~~

## 分层的理解

Docker镜像都是只读，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下都叫镜像层

## commit

~~~shell
docker commit 提交容器成为一个新的副本
#命令和git原理类似
docker commit -m="提交的信息" -a="作者" 容器ID 目标镜像名:[TAG]
~~~

**实际操作**

~~~shell
#首先启动一个官方的tomcat镜像

#发现tomcat镜像webapps没有任何文件

#自己进行复制文件从webapps.dist-->webapps

#通过commit提交修改后的镜像，生成新的镜像，这个镜像就是新的镜像
[root@iZfdjfsqewlu0jZ ~]# docker commit -a="liyouxiu" -m="add webapps files" 3d000cb06218 retomcat:1.0 
sha256:a84aeaeee34f846c628058722c677c4c55967fec64469c2a83d4ef7de887f7e8
[root@iZfdjfsqewlu0jZ ~]# docker images
REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
retomcat              1.0       a84aeaeee34f   20 seconds ago   684MB
nginx                 latest    605c77e624dd   5 months ago     141MB
tomcat                9.0       b8e65a4d736d   5 months ago     680MB
tomcat                latest    fb5657adc892   5 months ago     680MB
redis                 latest    7614ae9453d1   5 months ago     113MB
mysql                 5.7       c20987f18b13   5 months ago     448MB
mysql                 latest    3218b38490ce   5 months ago     516MB
hello-world           latest    feb5d9fea6a5   8 months ago     13.3kB
centos                latest    5d0da3dc9764   9 months ago     231MB
portainer/portainer   latest    580c0e4e98b0   15 months ago    79.1MB
elasticsearch         latest    5acf0e8da90b   3 years ago      486MB

~~~

# 容器数据卷

## 什么是容器数据卷

容器之间有一个数据共享技术！Docker容器中产生的数据，同步到本地！这就是卷技术，目录的挂载，我们将容器内的目录，挂载到linux中

**总结一句话：容器的持久化和同步操作，容器间也可以进行数据共享**

## 使用数据卷

> 方式一使用命令来挂载 -v

~~~shell
docker run -it -v 主机目录:容器内目录
~~~

~~~shell
#测试
[root@iZfdjfsqewlu0jZ home]# docker run -it -v /home/Test00:/home centos /bin/bash

#查看详情
[root@iZfdjfsqewlu0jZ Test00]# docker inspect 7b5b225c937d
[
    {
        "Id": "7b5b225c937da2f62d8549f388cd2c7a9138ebe822b9492aae33ab4509c69bff",
        "Created": "2022-06-23T02:28:08.783347075Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 3315695,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-06-23T02:28:09.239893528Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/7b5b225c937da2f62d8549f388cd2c7a9138ebe822b9492aae33ab4509c69bff/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/7b5b225c937da2f62d8549f388cd2c7a9138ebe822b9492aae33ab4509c69bff/hostname",
        "HostsPath": "/var/lib/docker/containers/7b5b225c937da2f62d8549f388cd2c7a9138ebe822b9492aae33ab4509c69bff/hosts",
        "LogPath": "/var/lib/docker/containers/7b5b225c937da2f62d8549f388cd2c7a9138ebe822b9492aae33ab4509c69bff/7b5b225c937da2f62d8549f388cd2c7a9138ebe822b9492aae33ab4509c69bff-json.log",
        "Name": "/peaceful_shannon",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "/home/Test00:/home"
            ],
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
                "LowerDir": "/var/lib/docker/overlay2/16a4230beea64dbb849e2f41a26bb6924fad0b1088a441219a9835c12168a23c-init/diff:/var/lib/docker/overlay2/8c257a1ce0d2cc0a0b6de32a4852b90742f8dd8ba3282b58a7b0c657d0044a35/diff",
                "MergedDir": "/var/lib/docker/overlay2/16a4230beea64dbb849e2f41a26bb6924fad0b1088a441219a9835c12168a23c/merged",
                "UpperDir": "/var/lib/docker/overlay2/16a4230beea64dbb849e2f41a26bb6924fad0b1088a441219a9835c12168a23c/diff",
                "WorkDir": "/var/lib/docker/overlay2/16a4230beea64dbb849e2f41a26bb6924fad0b1088a441219a9835c12168a23c/work"
            },
            "Name": "overlay2"
        },
-----------------------------------------------------------------------------------------------------------
 挂载详情
 "Mounts": [
            {
                "Type": "bind",
                "Source": "/home/Test00",   主机地址
                "Destination": "/home",	    docker容器内的地址
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
-----------------------------------------------------------------------------------------------------------
        "Config": {
            "Hostname": "7b5b225c937d",
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
            "SandboxID": "8d342af8af78353d2680abd62de4c8a886fd108a8cc86b25bc0bec39bfaa7eb6",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/8d342af8af78",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "7ea08569fb0be809b0c051494635273b9a9ba99f66b92f5bcd386fab249bde54",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.5",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:05",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "9dbbbcb0d160f07a52704382b4cf4495417afc0b572ee873704744b77f0ec8f6",
                    "EndpointID": "7ea08569fb0be809b0c051494635273b9a9ba99f66b92f5bcd386fab249bde54",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.5",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:05",
                    "DriverOpts": null
                }
            }
        }
    }
]

~~~

反向测试

~~~
1.停止容器，往真实机中写入数据

2.开始容器，查看数据已经被写入了本地文件中
~~~

**好处：我们以后修改只需要在本地修改即可，容器内会自动同步**

## 实战：部署mysql

思考：mysql数据持久化问题

~~~shell
#获取镜像

#运行容器，需要做数据挂载（安装启动mysql需要配置密码）
#官方测试 docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
[root@iZfdjfsqewlu0jZ /]# docker run  -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home//mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql00 mysql:5.7
20bb428b3abc6fc4a2d02f420ab5438f1984ecb25ca15f6754e14f71b3aaf25b

#docker run（启动容器）  -d（后台运行） -p 3310:3306（端口映射） -v /home/mysql/conf:/etc/mysql/conf.d -v /home//mysql/data:/var/lib/mysql（两个都是目录映射） -e MYSQL_ROOT_PASSWORD=123456（设置密码） --name mysql00（设置密码）mysql:5.7（镜像名字：版本）


~~~

测试连接

![](https://pic.imgdb.cn/item/62b3ed9309475431297b3f9f.jpg)

~~~shell
#创建一个数据库，查看映射是否成功
~~~

## 具名和匿名挂载

~~~shell
#匿名挂载
-v 容器内路径
docker run -d -P --name nginx01 -v /etc/nginx nginx

#查看卷的信息
[root@iZfdjfsqewlu0jZ /]# docker volume --help

Usage:  docker volume COMMAND

Manage volumes

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes

Run 'docker volume COMMAND --help' for more information on a command.
#查看卷的具体信息
[root@iZfdjfsqewlu0jZ /]# docker volume ls
DRIVER    VOLUME NAME
local     c939aca226928b72baaac8259f429d48f6c0fbddecce846010f1dab20502b67b
local     e95d7c04701d510efb088ca06e019652c7be250021c4dc52a2c6428905bfa919
#↑这个就是匿名挂载只有只有容器内的路径，没有容器外的路径

#具名挂载
[root@iZfdjfsqewlu0jZ /]# docker run -d -P --name nginx03 -v juming-nginx:/etc/nginx nginx
14b6abc6ad616dcf8eb1220bd6d6d65504f29b14c7e5e565ff8816bc52a72690
[root@iZfdjfsqewlu0jZ /]# docker volume ls
DRIVER    VOLUME NAME
local     c939aca226928b72baaac8259f429d48f6c0fbddecce846010f1dab20502b67b
local     e95d7c04701d510efb088ca06e019652c7be250021c4dc52a2c6428905bfa919
#具名挂载卷
local     juming-nginx
#查看下具体的卷的对应信息
[root@iZfdjfsqewlu0jZ /]# docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2022-06-23T16:05:31+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]

~~~

**所有docker未指定卷的的情况下，目录默认都是 /var/lib/docker/volumes/xxxx/_data 下**

总结：我们大多情况下使用的都是具名挂载，这样可以方便的让我们找到卷的位置

~~~shell
#如何确定是具名挂载还是匿名挂载或者指定挂载
-v 卷名 #匿名挂载
-v 名字:卷名#具名挂载
-v 卷名:卷名#指定挂载
~~~

拓展

~~~shell
#通过-v juming-nginx:/etc/nginx:ro或者rw改变读写权限
#ro==readonly只读
#rw==readwrite可读可写
#一旦设置了权限，我们挂载出来的内容就有了限定
[root@iZfdjfsqewlu0jZ /]# docker run -d -P --name nginx03 -v juming-nginx:/etc/nginx:ro nginx
[root@iZfdjfsqewlu0jZ /]# docker run -d -P --name nginx03 -v juming-nginx:/etc/nginx:rw nginx
#ro只能通过宿主机来改变
#rw容器内也可进行改变
~~~

## 初识Docker Files

Docker Files就是用来构建docker镜像的构建文件！命令和脚本

通过脚本可以生成镜像，镜像是一层一层的，脚本的每一个命令都是一层

~~~shell
#测试
#创建一个dockerfile
#文件中的内容
#格式 指令（大写） 参数
FROM centos
VOLUME ["volume01","volume02"]
CMD echo "----emd-----"
CMD /bin/bash
#这里的每个命令就是一层

#生成自己的镜像
[root@iZfdjfsqewlu0jZ docker-test-volume]# docker build -f dockerFile1 -t liyouxiu/centos .
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM centos
 ---> 5d0da3dc9764
Step 2/4 : VOLUME ["volume01","volume02"]
 ---> Running in 14c1ce5e35c9
Removing intermediate container 14c1ce5e35c9
 ---> 09e0dfe6348e
Step 3/4 : CMD echo "----end-----"
 ---> Running in cfb7b96150cd
Removing intermediate container cfb7b96150cd
 ---> c352920bfd7c
Step 4/4 : CMD /bin/bash
 ---> Running in 4dcfea657aaa
Removing intermediate container 4dcfea657aaa
 ---> d6f169f35ab0
Successfully built d6f169f35ab0
Successfully tagged liyouxiu/centos:latest

~~~

~~~shell
#启动自己的容器
[root@b359ddb1f156 /]# ls  -l
total 0
lrwxrwxrwx   1 root root   7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root 360 Jun 27 14:13 dev
drwxr-xr-x   1 root root  66 Jun 27 14:13 etc
drwxr-xr-x   2 root root   6 Nov  3  2020 home
lrwxrwxrwx   1 root root   7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root   9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root   6 Sep 15  2021 lost+found
drwxr-xr-x   2 root root   6 Nov  3  2020 media
drwxr-xr-x   2 root root   6 Nov  3  2020 mnt
drwxr-xr-x   2 root root   6 Nov  3  2020 opt
dr-xr-xr-x 162 root root   0 Jun 27 14:13 proc
dr-xr-x---   2 root root 162 Sep 15  2021 root
drwxr-xr-x  11 root root 163 Sep 15  2021 run
lrwxrwxrwx   1 root root   8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root   6 Nov  3  2020 srv
dr-xr-xr-x  13 root root   0 Jun 14 07:06 sys
drwxrwxrwt   7 root root 171 Sep 15  2021 tmp
drwxr-xr-x  12 root root 144 Sep 15  2021 usr
drwxr-xr-x  20 root root 262 Sep 15  2021 var
drwxr-xr-x   2 root root   6 Jun 27 14:13 volume01（这两个就是生成镜像时自动挂载的目录）数据卷目录
drwxr-xr-x   2 root root   6 Jun 27 14:13 volume02（这两个就是生成镜像时自动挂载的目录）数据卷目录

#这个卷一定是和外部同步的目录
[root@iZfdjfsqewlu0jZ ~]# docker inspect 5445e532a850
[
    {
        "Id": "5445e532a85078590164b00bae041f60b56e5a2e989e76668c363a59b3643dc4",
        "Created": "2022-06-27T14:20:35.319474947Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 2103703,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-06-27T14:20:35.837574249Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:d6f169f35ab0b8698d1751a02dc08a19999c1532a52c1249564fd4caddf8792c",
        "ResolvConfPath": "/var/lib/docker/containers/5445e532a85078590164b00bae041f60b56e5a2e989e76668c363a59b3643dc4/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/5445e532a85078590164b00bae041f60b56e5a2e989e76668c363a59b3643dc4/hostname",
        "HostsPath": "/var/lib/docker/containers/5445e532a85078590164b00bae041f60b56e5a2e989e76668c363a59b3643dc4/hosts",
        "LogPath": "/var/lib/docker/containers/5445e532a85078590164b00bae041f60b56e5a2e989e76668c363a59b3643dc4/5445e532a85078590164b00bae041f60b56e5a2e989e76668c363a59b3643dc4-json.log",
        "Name": "/heuristic_rubin",
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
                "LowerDir": "/var/lib/docker/overlay2/6e4f29c3d39129c169f1d0fef30a153f041f5c2cac1d406a20d41fbce21e410b-init/diff:/var/lib/docker/overlay2/8c257a1ce0d2cc0a0b6de32a4852b90742f8dd8ba3282b58a7b0c657d0044a35/diff",
                "MergedDir": "/var/lib/docker/overlay2/6e4f29c3d39129c169f1d0fef30a153f041f5c2cac1d406a20d41fbce21e410b/merged",
                "UpperDir": "/var/lib/docker/overlay2/6e4f29c3d39129c169f1d0fef30a153f041f5c2cac1d406a20d41fbce21e410b/diff",
                "WorkDir": "/var/lib/docker/overlay2/6e4f29c3d39129c169f1d0fef30a153f041f5c2cac1d406a20d41fbce21e410b/work"
            },
            "Name": "overlay2"
        },
-------------------------------------------------------------------------------------------------------      #个人的挂载目录  
        "Mounts": [
            {
                "Type": "volume",
                "Name": "99a06c0cbf224897ea6859c727e6b289d17e17012cae43cfa8cbf0e39367642c",
                "Source": "/var/lib/docker/volumes/99a06c0cbf224897ea6859c727e6b289d17e17012cae43cfa8cbf0e39367642c/_data",
                "Destination": "volume01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "96d7e44947c3f1dbefbf96532c8a6fa394e999930ba225b6317fff2d7885bb0c",
                "Source": "/var/lib/docker/volumes/96d7e44947c3f1dbefbf96532c8a6fa394e999930ba225b6317fff2d7885bb0c/_data",
                "Destination": "volume02",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
-------------------------------------------------------------------------------------------------------
        "Config": {
            "Hostname": "5445e532a850",
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
            "Image": "liyouxiu/centos",
            "Volumes": {
                "volume01": {},
                "volume02": {}
            },
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
            "SandboxID": "169d2ec7d1037186972ac4e6a410a8d1ffc8ca3a1acc74e9de2f6ff668e6a6ed",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/169d2ec7d103",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "8bbda1c4c2e3440000161611eaf819a8b56e8c6923da69a056270e88269c0352",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.9",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:09",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "9dbbbcb0d160f07a52704382b4cf4495417afc0b572ee873704744b77f0ec8f6",
                    "EndpointID": "8bbda1c4c2e3440000161611eaf819a8b56e8c6923da69a056270e88269c0352",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.9",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:09",
                    "DriverOpts": null
                }
            }
        }
    }
]

~~~

这种方式未来使用的非常多，因为我们会构建自己的镜像，假设构建镜像的时候没有挂载卷，要手动镜像挂载 -v 卷名：容器内路径

## 数据卷容器

两个或多个容器内数据共享，多个容器数据同步

![](https://pic.imgdb.cn/item/62baa2471d64b0706678952d.jpg)

~~~shell
#测试
#启动三个镜像容器
#启动父镜像
[root@iZfdjfsqewlu0jZ ~]# docker run -it --name docker01 liyouxiu/centos
[root@717305d6a0a9 /]# ls -l
total 0
lrwxrwxrwx   1 root root   7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root 360 Jun 28 06:44 dev
drwxr-xr-x   1 root root  66 Jun 28 06:44 etc
drwxr-xr-x   2 root root   6 Nov  3  2020 home
lrwxrwxrwx   1 root root   7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root   9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root   6 Sep 15  2021 lost+found
drwxr-xr-x   2 root root   6 Nov  3  2020 media
drwxr-xr-x   2 root root   6 Nov  3  2020 mnt
drwxr-xr-x   2 root root   6 Nov  3  2020 opt
dr-xr-xr-x 133 root root   0 Jun 28 06:44 proc
dr-xr-x---   2 root root 162 Sep 15  2021 root
drwxr-xr-x  11 root root 163 Sep 15  2021 run
lrwxrwxrwx   1 root root   8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root   6 Nov  3  2020 srv
dr-xr-xr-x  13 root root   0 Jun 14 07:06 sys
drwxrwxrwt   7 root root 171 Sep 15  2021 tmp
drwxr-xr-x  12 root root 144 Sep 15  2021 usr
drwxr-xr-x  20 root root 262 Sep 15  2021 var
drwxr-xr-x   2 root root   6 Jun 28 06:44 volume01
drwxr-xr-x   2 root root   6 Jun 28 06:44 volume02
#启动另一个镜像继承父镜像数据
[root@iZfdjfsqewlu0jZ ~]# docker run -it --name docker02 --volumes-from docker01 liyouxiu/centos
[root@e7e09aa006c9 /]# ls -l
total 0
lrwxrwxrwx   1 root root   7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root 360 Jun 28 06:47 dev
drwxr-xr-x   1 root root  66 Jun 28 06:47 etc
drwxr-xr-x   2 root root   6 Nov  3  2020 home
lrwxrwxrwx   1 root root   7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root   9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root   6 Sep 15  2021 lost+found
drwxr-xr-x   2 root root   6 Nov  3  2020 media
drwxr-xr-x   2 root root   6 Nov  3  2020 mnt
drwxr-xr-x   2 root root   6 Nov  3  2020 opt
dr-xr-xr-x 131 root root   0 Jun 28 06:47 proc
dr-xr-x---   2 root root 162 Sep 15  2021 root
drwxr-xr-x  11 root root 163 Sep 15  2021 run
lrwxrwxrwx   1 root root   8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root   6 Nov  3  2020 srv
dr-xr-xr-x  13 root root   0 Jun 14 07:06 sys
drwxrwxrwt   7 root root 171 Sep 15  2021 tmp
drwxr-xr-x  12 root root 144 Sep 15  2021 usr
drwxr-xr-x  20 root root 262 Sep 15  2021 var
drwxr-xr-x   2 root root   6 Jun 28 06:44 volume01
drwxr-xr-x   2 root root   6 Jun 28 06:44 volume02

#测试数据同步
在父容器中创建文件可以在另一个容器中看到
#父容器
[root@iZfdjfsqewlu0jZ ~]# docker attach docker01
[root@717305d6a0a9 /]# cd volume01
[root@717305d6a0a9 volume01]# touch UpData.txt
[root@717305d6a0a9 volume01]# vi UpData.txt 
[root@717305d6a0a9 volume01]# cat UpData.txt 
shujutongbuceshi 
2022.6.28

#子容器
[root@e7e09aa006c9 /]# cd volume01
[root@e7e09aa006c9 volume01]# ls
UpData.txt
[root@e7e09aa006c9 volume01]# cat UpData.txt 
shujutongbuceshi 
2022.6.28

#数据已同步
#数据的同步还是双向的
~~~

删除父容器之后其他的容器的数据不会丢失，因为这是卷的挂载

# Docker Files

## Docker file介绍

Dockerfile是用来构建docker镜像的文件！命令参数脚本

构建步骤

1.编写有一个dockerfile文件

2.docker build构建成为一个镜像

3.docker run运行镜像

4.docker push发布镜像（docker hub，阿里云镜像仓库）

## Docker file的构建过程

**基础知识**

1.每个关键字都是大写字母

2.执行是从上到下顺序执行的

3.#表示注释

4.每一个指令都会创建提交一个新的镜像，并提交

![](https://pic.imgdb.cn/item/62bb00e41d64b0706606159b.jpg)

docker file是面向开发的，我们以后要发布项目，作镜像，就要编写docker file！

Docker镜像逐渐成为了企业的交付标准

步骤：开发，部署，运维

Docker file：构建文件，定义了一切步骤，源代码

Docker images：通过Docker file构建生成的镜像，最终发布和运行的产品

Docker容器：容器就是镜像运行起来提供服务

## Docker file指令

```shell
FROM        #基础镜像，一切从这里开始构建
MAINTAINER  #镜像是谁写的 姓名+邮箱
RUN         #镜像构建的时候需要运行的命令
ADD			#步骤，tomcat镜像，这个tomcat的压缩包！添加内容
WORKDIR		#镜像的工作目录
VOLUME		#挂载的目录位置
EXPOSE		#暴露端口配置
CMD 		#指定这个容器启动时运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT	#指定这个容器启动时运行的命令，可以追加命令
ONBUILD		#当构建一个被继承DockerFile这个时候就会运行ONBUILD指令，触发指令
COPY		#将我们的文件拷贝到镜像中
ENV			#构建的时候设置环境变量
```



![](https://pic.imgdb.cn/item/62bb0bed1d64b07066170d01.jpg)

## 测试

Docker Hub中大多数的镜像都是来自FROM scratch

![](https://pic.imgdb.cn/item/62bb0f431d64b070661b7386.jpg)

> 创建一个自己的centos

~~~shell
#编写Docker File文件
[root@iZfdjfsqewlu0jZ DockerFile]# cat MyDockerFile_Centos00 
FROM centos:7
MAINTAINER liyouxiu<sunsunsun5675@foxmail.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80
CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash

#生成镜像
#可能出现的错误：centos8维护时间较短所以可以把版本改成centos:7
[root@iZfdjfsqewlu0jZ DockerFile]# docker build -f MyDockerFile_Centos00 -t mycentos:0.1 .
Sending build context to Docker daemon  2.048kB
Step 1/10 : FROM centos:7
7: Pulling from library/centos
2d473b07cdd5: Pull complete 
Digest: sha256:9d4bcbbb213dfd745b58be38b13b996ebb5ac315fe75711bd618426a630e0987
Status: Downloaded newer image for centos:7
 ---> eeb6ee3f44bd
Step 2/10 : MAINTAINER liyouxiu<sunsunsun5675@foxmail.com>
 ---> Running in bd322c57861a
Removing intermediate container bd322c57861a
 ---> 01276ac464a7
Step 3/10 : ENV MYPATH /usr/local
 ---> Running in a9305e1df382
Removing intermediate container a9305e1df382
 ---> adccc12c90d6
Step 4/10 : WORKDIR $MYPATH
 ---> Running in aeeb78e1af6e
Removing intermediate container aeeb78e1af6e
 ---> fd4b70bed1a0
Step 5/10 : RUN yum -y install vim
 ---> Running in 72e52e53329e
Loaded plugins: fastestmirror, ovl
Determining fastest mirrors
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Resolving Dependencies
--> Running transaction check
---> Package vim-enhanced.x86_64 2:7.4.629-8.el7_9 will be installed
--> Processing Dependency: vim-common = 2:7.4.629-8.el7_9 for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Processing Dependency: which for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Processing Dependency: perl(:MODULE_COMPAT_5.16.3) for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Processing Dependency: libperl.so()(64bit) for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Processing Dependency: libgpm.so.2()(64bit) for package: 2:vim-enhanced-7.4.629-8.el7_9.x86_64
--> Running transaction check
---> Package gpm-libs.x86_64 0:1.20.7-6.el7 will be installed
---> Package perl.x86_64 4:5.16.3-299.el7_9 will be installed
--> Processing Dependency: perl(Socket) >= 1.3 for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Scalar::Util) >= 1.10 for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl-macros for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(threads::shared) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(threads) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(constant) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Time::Local) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Time::HiRes) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Storable) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Socket) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Scalar::Util) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Pod::Simple::XHTML) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Pod::Simple::Search) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Getopt::Long) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Filter::Util::Call) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Temp) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Spec::Unix) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Spec::Functions) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Spec) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(File::Path) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Exporter) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Cwd) for package: 4:perl-5.16.3-299.el7_9.x86_64
--> Processing Dependency: perl(Carp) for package: 4:perl-5.16.3-299.el7_9.x86_64
---> Package perl-libs.x86_64 4:5.16.3-299.el7_9 will be installed
---> Package vim-common.x86_64 2:7.4.629-8.el7_9 will be installed
--> Processing Dependency: vim-filesystem for package: 2:vim-common-7.4.629-8.el7_9.x86_64
---> Package which.x86_64 0:2.20-7.el7 will be installed
--> Running transaction check
---> Package perl-Carp.noarch 0:1.26-244.el7 will be installed
---> Package perl-Exporter.noarch 0:5.68-3.el7 will be installed
---> Package perl-File-Path.noarch 0:2.09-2.el7 will be installed
---> Package perl-File-Temp.noarch 0:0.23.01-3.el7 will be installed
---> Package perl-Filter.x86_64 0:1.49-3.el7 will be installed
---> Package perl-Getopt-Long.noarch 0:2.40-3.el7 will be installed
--> Processing Dependency: perl(Pod::Usage) >= 1.14 for package: perl-Getopt-Long-2.40-3.el7.noarch
--> Processing Dependency: perl(Text::ParseWords) for package: perl-Getopt-Long-2.40-3.el7.noarch
---> Package perl-PathTools.x86_64 0:3.40-5.el7 will be installed
---> Package perl-Pod-Simple.noarch 1:3.28-4.el7 will be installed
--> Processing Dependency: perl(Pod::Escapes) >= 1.04 for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
--> Processing Dependency: perl(Encode) for package: 1:perl-Pod-Simple-3.28-4.el7.noarch
---> Package perl-Scalar-List-Utils.x86_64 0:1.27-248.el7 will be installed
---> Package perl-Socket.x86_64 0:2.010-5.el7 will be installed
---> Package perl-Storable.x86_64 0:2.45-3.el7 will be installed
---> Package perl-Time-HiRes.x86_64 4:1.9725-3.el7 will be installed
---> Package perl-Time-Local.noarch 0:1.2300-2.el7 will be installed
---> Package perl-constant.noarch 0:1.27-2.el7 will be installed
---> Package perl-macros.x86_64 4:5.16.3-299.el7_9 will be installed
---> Package perl-threads.x86_64 0:1.87-4.el7 will be installed
---> Package perl-threads-shared.x86_64 0:1.43-6.el7 will be installed
---> Package vim-filesystem.x86_64 2:7.4.629-8.el7_9 will be installed
--> Running transaction check
---> Package perl-Encode.x86_64 0:2.51-7.el7 will be installed
---> Package perl-Pod-Escapes.noarch 1:1.04-299.el7_9 will be installed
---> Package perl-Pod-Usage.noarch 0:1.63-3.el7 will be installed
--> Processing Dependency: perl(Pod::Text) >= 3.15 for package: perl-Pod-Usage-1.63-3.el7.noarch
--> Processing Dependency: perl-Pod-Perldoc for package: perl-Pod-Usage-1.63-3.el7.noarch
---> Package perl-Text-ParseWords.noarch 0:3.29-4.el7 will be installed
--> Running transaction check
---> Package perl-Pod-Perldoc.noarch 0:3.20-4.el7 will be installed
--> Processing Dependency: perl(parent) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
--> Processing Dependency: perl(HTTP::Tiny) for package: perl-Pod-Perldoc-3.20-4.el7.noarch
--> Processing Dependency: groff-base for package: perl-Pod-Perldoc-3.20-4.el7.noarch
---> Package perl-podlators.noarch 0:2.5.1-3.el7 will be installed
--> Running transaction check
---> Package groff-base.x86_64 0:1.22.2-8.el7 will be installed
---> Package perl-HTTP-Tiny.noarch 0:0.033-3.el7 will be installed
---> Package perl-parent.noarch 1:0.225-244.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                    Arch       Version                Repository   Size
================================================================================
Installing:
 vim-enhanced               x86_64     2:7.4.629-8.el7_9      updates     1.1 M
Installing for dependencies:
 gpm-libs                   x86_64     1.20.7-6.el7           base         32 k
 groff-base                 x86_64     1.22.2-8.el7           base        942 k
 perl                       x86_64     4:5.16.3-299.el7_9     updates     8.0 M
 perl-Carp                  noarch     1.26-244.el7           base         19 k
 perl-Encode                x86_64     2.51-7.el7             base        1.5 M
 perl-Exporter              noarch     5.68-3.el7             base         28 k
 perl-File-Path             noarch     2.09-2.el7             base         26 k
 perl-File-Temp             noarch     0.23.01-3.el7          base         56 k
 perl-Filter                x86_64     1.49-3.el7             base         76 k
 perl-Getopt-Long           noarch     2.40-3.el7             base         56 k
 perl-HTTP-Tiny             noarch     0.033-3.el7            base         38 k
 perl-PathTools             x86_64     3.40-5.el7             base         82 k
 perl-Pod-Escapes           noarch     1:1.04-299.el7_9       updates      52 k
 perl-Pod-Perldoc           noarch     3.20-4.el7             base         87 k
 perl-Pod-Simple            noarch     1:3.28-4.el7           base        216 k
 perl-Pod-Usage             noarch     1.63-3.el7             base         27 k
 perl-Scalar-List-Utils     x86_64     1.27-248.el7           base         36 k
 perl-Socket                x86_64     2.010-5.el7            base         49 k
 perl-Storable              x86_64     2.45-3.el7             base         77 k
 perl-Text-ParseWords       noarch     3.29-4.el7             base         14 k
 perl-Time-HiRes            x86_64     4:1.9725-3.el7         base         45 k
 perl-Time-Local            noarch     1.2300-2.el7           base         24 k
 perl-constant              noarch     1.27-2.el7             base         19 k
 perl-libs                  x86_64     4:5.16.3-299.el7_9     updates     690 k
 perl-macros                x86_64     4:5.16.3-299.el7_9     updates      44 k
 perl-parent                noarch     1:0.225-244.el7        base         12 k
 perl-podlators             noarch     2.5.1-3.el7            base        112 k
 perl-threads               x86_64     1.87-4.el7             base         49 k
 perl-threads-shared        x86_64     1.43-6.el7             base         39 k
 vim-common                 x86_64     2:7.4.629-8.el7_9      updates     5.9 M
 vim-filesystem             x86_64     2:7.4.629-8.el7_9      updates      11 k
 which                      x86_64     2.20-7.el7             base         41 k

Transaction Summary
================================================================================
Install  1 Package (+32 Dependent packages)

Total download size: 19 M
Installed size: 63 M
Downloading packages:
warning: /var/cache/yum/x86_64/7/base/packages/gpm-libs-1.20.7-6.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Public key for gpm-libs-1.20.7-6.el7.x86_64.rpm is not installed
Public key for perl-5.16.3-299.el7_9.x86_64.rpm is not installed
--------------------------------------------------------------------------------
Total                                               17 MB/s |  19 MB  00:01     
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-9.2009.0.el7.centos.x86_64 (@CentOS)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : gpm-libs-1.20.7-6.el7.x86_64                                1/33 
  Installing : 2:vim-filesystem-7.4.629-8.el7_9.x86_64                     2/33 
  Installing : 2:vim-common-7.4.629-8.el7_9.x86_64                         3/33 
  Installing : which-2.20-7.el7.x86_64                                     4/33 
install-info: No such file or directory for /usr/share/info/which.info.gz
  Installing : groff-base-1.22.2-8.el7.x86_64                              5/33 
  Installing : 1:perl-parent-0.225-244.el7.noarch                          6/33 
  Installing : perl-HTTP-Tiny-0.033-3.el7.noarch                           7/33 
  Installing : perl-podlators-2.5.1-3.el7.noarch                           8/33 
  Installing : perl-Pod-Perldoc-3.20-4.el7.noarch                          9/33 
  Installing : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                   10/33 
  Installing : perl-Encode-2.51-7.el7.x86_64                              11/33 
  Installing : perl-Text-ParseWords-3.29-4.el7.noarch                     12/33 
  Installing : perl-Pod-Usage-1.63-3.el7.noarch                           13/33 
  Installing : 4:perl-macros-5.16.3-299.el7_9.x86_64                      14/33 
  Installing : perl-Storable-2.45-3.el7.x86_64                            15/33 
  Installing : perl-Exporter-5.68-3.el7.noarch                            16/33 
  Installing : perl-constant-1.27-2.el7.noarch                            17/33 
  Installing : perl-Socket-2.010-5.el7.x86_64                             18/33 
  Installing : perl-Time-Local-1.2300-2.el7.noarch                        19/33 
  Installing : perl-Carp-1.26-244.el7.noarch                              20/33 
  Installing : perl-PathTools-3.40-5.el7.x86_64                           21/33 
  Installing : perl-Scalar-List-Utils-1.27-248.el7.x86_64                 22/33 
  Installing : 1:perl-Pod-Simple-3.28-4.el7.noarch                        23/33 
  Installing : perl-File-Temp-0.23.01-3.el7.noarch                        24/33 
  Installing : perl-File-Path-2.09-2.el7.noarch                           25/33 
  Installing : perl-threads-shared-1.43-6.el7.x86_64                      26/33 
  Installing : perl-threads-1.87-4.el7.x86_64                             27/33 
  Installing : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                      28/33 
  Installing : perl-Filter-1.49-3.el7.x86_64                              29/33 
  Installing : 4:perl-libs-5.16.3-299.el7_9.x86_64                        30/33 
  Installing : perl-Getopt-Long-2.40-3.el7.noarch                         31/33 
  Installing : 4:perl-5.16.3-299.el7_9.x86_64                             32/33 
  Installing : 2:vim-enhanced-7.4.629-8.el7_9.x86_64                      33/33 
  Verifying  : perl-HTTP-Tiny-0.033-3.el7.noarch                           1/33 
  Verifying  : perl-threads-shared-1.43-6.el7.x86_64                       2/33 
  Verifying  : perl-Storable-2.45-3.el7.x86_64                             3/33 
  Verifying  : groff-base-1.22.2-8.el7.x86_64                              4/33 
  Verifying  : perl-Exporter-5.68-3.el7.noarch                             5/33 
  Verifying  : perl-constant-1.27-2.el7.noarch                             6/33 
  Verifying  : perl-PathTools-3.40-5.el7.x86_64                            7/33 
  Verifying  : 4:perl-macros-5.16.3-299.el7_9.x86_64                       8/33 
  Verifying  : 2:vim-enhanced-7.4.629-8.el7_9.x86_64                       9/33 
  Verifying  : 1:perl-parent-0.225-244.el7.noarch                         10/33 
  Verifying  : perl-Socket-2.010-5.el7.x86_64                             11/33 
  Verifying  : which-2.20-7.el7.x86_64                                    12/33 
  Verifying  : 2:vim-filesystem-7.4.629-8.el7_9.x86_64                    13/33 
  Verifying  : perl-File-Temp-0.23.01-3.el7.noarch                        14/33 
  Verifying  : 1:perl-Pod-Simple-3.28-4.el7.noarch                        15/33 
  Verifying  : perl-Time-Local-1.2300-2.el7.noarch                        16/33 
  Verifying  : 1:perl-Pod-Escapes-1.04-299.el7_9.noarch                   17/33 
  Verifying  : perl-Carp-1.26-244.el7.noarch                              18/33 
  Verifying  : 2:vim-common-7.4.629-8.el7_9.x86_64                        19/33 
  Verifying  : perl-Scalar-List-Utils-1.27-248.el7.x86_64                 20/33 
  Verifying  : perl-Pod-Usage-1.63-3.el7.noarch                           21/33 
  Verifying  : perl-Encode-2.51-7.el7.x86_64                              22/33 
  Verifying  : perl-Pod-Perldoc-3.20-4.el7.noarch                         23/33 
  Verifying  : perl-podlators-2.5.1-3.el7.noarch                          24/33 
  Verifying  : 4:perl-5.16.3-299.el7_9.x86_64                             25/33 
  Verifying  : perl-File-Path-2.09-2.el7.noarch                           26/33 
  Verifying  : perl-threads-1.87-4.el7.x86_64                             27/33 
  Verifying  : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                      28/33 
  Verifying  : gpm-libs-1.20.7-6.el7.x86_64                               29/33 
  Verifying  : perl-Filter-1.49-3.el7.x86_64                              30/33 
  Verifying  : perl-Getopt-Long-2.40-3.el7.noarch                         31/33 
  Verifying  : perl-Text-ParseWords-3.29-4.el7.noarch                     32/33 
  Verifying  : 4:perl-libs-5.16.3-299.el7_9.x86_64                        33/33 

Installed:
  vim-enhanced.x86_64 2:7.4.629-8.el7_9                                         

Dependency Installed:
  gpm-libs.x86_64 0:1.20.7-6.el7                                                
  groff-base.x86_64 0:1.22.2-8.el7                                              
  perl.x86_64 4:5.16.3-299.el7_9                                                
  perl-Carp.noarch 0:1.26-244.el7                                               
  perl-Encode.x86_64 0:2.51-7.el7                                               
  perl-Exporter.noarch 0:5.68-3.el7                                             
  perl-File-Path.noarch 0:2.09-2.el7                                            
  perl-File-Temp.noarch 0:0.23.01-3.el7                                         
  perl-Filter.x86_64 0:1.49-3.el7                                               
  perl-Getopt-Long.noarch 0:2.40-3.el7                                          
  perl-HTTP-Tiny.noarch 0:0.033-3.el7                                           
  perl-PathTools.x86_64 0:3.40-5.el7                                            
  perl-Pod-Escapes.noarch 1:1.04-299.el7_9                                      
  perl-Pod-Perldoc.noarch 0:3.20-4.el7                                          
  perl-Pod-Simple.noarch 1:3.28-4.el7                                           
  perl-Pod-Usage.noarch 0:1.63-3.el7                                            
  perl-Scalar-List-Utils.x86_64 0:1.27-248.el7                                  
  perl-Socket.x86_64 0:2.010-5.el7                                              
  perl-Storable.x86_64 0:2.45-3.el7                                             
  perl-Text-ParseWords.noarch 0:3.29-4.el7                                      
  perl-Time-HiRes.x86_64 4:1.9725-3.el7                                         
  perl-Time-Local.noarch 0:1.2300-2.el7                                         
  perl-constant.noarch 0:1.27-2.el7                                             
  perl-libs.x86_64 4:5.16.3-299.el7_9                                           
  perl-macros.x86_64 4:5.16.3-299.el7_9                                         
  perl-parent.noarch 1:0.225-244.el7                                            
  perl-podlators.noarch 0:2.5.1-3.el7                                           
  perl-threads.x86_64 0:1.87-4.el7                                              
  perl-threads-shared.x86_64 0:1.43-6.el7                                       
  vim-common.x86_64 2:7.4.629-8.el7_9                                           
  vim-filesystem.x86_64 2:7.4.629-8.el7_9                                       
  which.x86_64 0:2.20-7.el7                                                     

Complete!
Removing intermediate container 72e52e53329e
 ---> b942a0b2690b
Step 6/10 : RUN yum -y install net-tools
 ---> Running in 8d2eea381d8a
Loaded plugins: fastestmirror, ovl
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Resolving Dependencies
--> Running transaction check
---> Package net-tools.x86_64 0:2.0-0.25.20131004git.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package         Arch         Version                          Repository  Size
================================================================================
Installing:
 net-tools       x86_64       2.0-0.25.20131004git.el7         base       306 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 306 k
Installed size: 917 k
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : net-tools-2.0-0.25.20131004git.el7.x86_64                    1/1 
  Verifying  : net-tools-2.0-0.25.20131004git.el7.x86_64                    1/1 

Installed:
  net-tools.x86_64 0:2.0-0.25.20131004git.el7                                   

Complete!
Removing intermediate container 8d2eea381d8a
 ---> 9d6083c62501
Step 7/10 : EXPOSE 80
 ---> Running in a391a5f4f8fe
Removing intermediate container a391a5f4f8fe
 ---> 98e846b16dcf
Step 8/10 : CMD echo $MYPATH
 ---> Running in bd99eda3b20e
Removing intermediate container bd99eda3b20e
 ---> 72c9477bf46f
Step 9/10 : CMD echo "---end---"
 ---> Running in bdcb94eb5dfd
Removing intermediate container bdcb94eb5dfd
 ---> ced40ebc13d3
Step 10/10 : CMD /bin/bash
 ---> Running in f21389152c0d
Removing intermediate container f21389152c0d
 ---> bd8f2448df1a
Successfully built bd8f2448df1a
Successfully tagged mycentos:0.1
~~~

~~~shell
#查看构建过程
[root@iZfdjfsqewlu0jZ ~]# docker history bd8f2448df1a
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
bd8f2448df1a   19 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/bin…   0B        
ced40ebc13d3   19 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B        
72c9477bf46f   19 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B        
98e846b16dcf   19 minutes ago   /bin/sh -c #(nop)  EXPOSE 80                    0B        
9d6083c62501   19 minutes ago   /bin/sh -c yum -y install net-tools             171MB     
b942a0b2690b   20 minutes ago   /bin/sh -c yum -y install vim                   226MB     
fd4b70bed1a0   20 minutes ago   /bin/sh -c #(nop) WORKDIR /usr/local            0B        
adccc12c90d6   20 minutes ago   /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0B        
01276ac464a7   20 minutes ago   /bin/sh -c #(nop)  MAINTAINER liyouxiu<sunsu…   0B        
eeb6ee3f44bd   9 months ago     /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
<missing>      9 months ago     /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B        
<missing>      9 months ago     /bin/sh -c #(nop) ADD file:b3ebbe8bd304723d4…   204MB     

~~~

> CMD和ENTRYPOINT的区别

~~~shell
CMD 		#指定这个容器启动时运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT	#指定这个容器启动时运行的命令，可以追加命令
~~~



# Docker 网络

# 未完待续