### 0x00 前言

因实验室内部需求，为实验室配置Git服务器一台，在此记录一下整个的流程，一是为了自己备忘，二是为了今后实验室成员学习使用

### 0x01 安装Ubuntu

实验室的服务器整体配置还是不错的，i3的CPU，4G内存，1T硬盘，配置个GitLab让实验室40个人用已经绰绰有余了，拿到手的是一台裸机。从[Ubuntu官网](https://www.ubuntu.com/server)下载到了一个Server版的Ubuntu16.04.2的镜像，Server版的没有图形化用户界面，而且仅安装了最基本的软件，所以只有700MB左右的大小。

用`UltraISO`将其刻录到一个U盘上，然后在服务器上安装就可以了（原谅一笔带过，因为安装的时候没拍摄安装过程）。

### 0x02 配置网络

实验室的服务器是静态独立IP，需要另加配置才能上网

```shell
root@ubuntu:~# vi /etc/network/interfaces
```

配置网卡信息如下

```shell
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
# This is an autoconfigured IPv6 interface
auto enp3s0
iface enp3s0 inet static	# static为静态模式,DHCP为自动获取
address *静态IP地址*	# 服务器静态IP地址
netmask 255.255.255.0	# 子网掩码
gateway *网关*	# 网关
dns-nameservers 114.114.114.114 	# DNS服务器
```

上述代码中星号包围的地方请自行咨询实验室负责人

重启网络服务

```shell
/etc/init.d/networking restart
```

接下来为了解决我天朝众所周知的网络原因，修改apt源地址

```shell
vi /etc/apt/sources.list
```

修改为（阿里云的源）：

```
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties
deb http://archive.canonical.com/ubuntu xenial partner
deb-src http://archive.canonical.com/ubuntu xenial partner
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse
```

然后更新源

```shell
apt-get update
```

### 0x03 安装docker

使用以下命令安装docker:

```shell
apt-get install docker.io
service docker restart  # 害怕没自动启动服务,所以装完总要重启一遍服务,我有强迫症
```

还是为了解决国内众所周知的网络原因，装一个加速器才能更好地连接[dockerhub](https://hub.docker.com)来pull镜像不是，在这里使用的是国内[daocloud](https://dashboard.daocloud.io/)的免费加速器，效果还算不错。

去[daocloud](https://dashboard.daocloud.io/)注册一个账号，然后接入自有主机，安装一个主机监控程序，他就会自动监测到然后添加主机，然后使用dao命令来代替docker就可以加速pull dockerhub上的镜像

### 0x04 安装GitLab

```shell
dao pull gitlab/gitlab-ce 	# pull镜像
docker images	# 查看本机上的镜像
# 运行镜像
docker run -d --publish 443:443 --publish 80:80 --publish 12110:22 --name gitlab --restart always 241
```

我在这里讲解一下运行镜像的各个参数的意义

```shell
docker run	就是用来启动一个容器
--publish 	用来做端口映射，宿主机端口:docker虚拟机端口
--name		为容器命名
--restart always	设置出问题后自动重启，如果你的容器本身是有问题的，设置了这一项之后会不断地重启，可好玩了
241			是image的id前三位，输入id的时候一般不用全输上，输入前几位用来区分开就行了
```

好了，现在去访问服务器的ip应该就可以看到gitlab的登陆页面了，一开始的工作是设置一个gitlab的root用户的密码

### 0x05 上传到我自己的网易蜂巢

众所周知的网络原因嘛，将这个下载好的镜像push到我自己的网易蜂巢，以便日后备用

```shell
docker login -u 网易蜂巢账号 -p 网易蜂巢密码 -e 邮箱 hub.c.163.com	# 登录
docker tag 241 hub.c.163.com/liuinstein/gitlab-ce	# 标记镜像
docker push hub.c.163.com/liuinstein/gitlab-ce	# 上传镜像
```

好了，以后大家可以使用命令来直接从我的网易蜂巢上pull镜像了

```shell
docker pull hub.c.163.com/liuinstein/gitlab-ce:latest
```

### 0x06 参考资料

[GitLab Docker images](https://docs.gitlab.com/omnibus/docker/#after-starting-a-container)

