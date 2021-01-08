## 0. 前序准备工作

> 本节主要对演示用的服务端和客户端进行各种初始化，长篇废话警告，各种重做警告，各种不按要求警告，快进到挂科（捂脸）。

宿主机为 Arch Linux，这里单方面宣布 Arch Linux 是世界上最好的 Linux 发行版。

由于 GNOME 唯一指定虚拟机 [Boxes](https://wiki.gnome.org/Apps/Boxes) 实在没有那么多功能（不是），采用 Podman 容器进行演示，验证安装（`-Q` 仅会在本地查询）：

```
$ pacman -Qi podman
```

从 DockerHub 拉取 CentOS 的镜像（RHEL 6 结束维护了 orz）：

```
# podman pull centos
```

为了方便容器间网络通信，创建新的 Bridge 网络（用默认的 `podman` 其实也可）：

```
# podman network create -d bridge final-rongjialin
```

创建两个实例，`rongjialin-client`  为客户端，`rongjialin-server` 为服务端，这里指定了 Bridge：

```
# podman run -it --name server-rongjialin --network final-rongjialin centos /bin/bash
# podman run -it --name client-rongjialin --network final-rongjialin centos /bin/bash
```

验证容器是否创建成功：

```
# podman ps -a
```

Tilix 开挂大法（捂脸），现在启动刚才创建的两个实例并进入（Tilix 上方为服务端，下方为客户端），以服务端为例：

```
# podman start 17e33a49557e && podman attach 17e33a49557e
```

查看本机 IP 地址：

```
# ip address
```

客户端尝试 PING 服务端：

```
# ping -c 3 10.89.0.13
```

**干！忘记还有各种 IP 要求了，重来重来（哭）。**

删网删实例跑路：

```
# podman network rm final-rongjialin -f
```

学乖了，指定一下网段：

```
# podman network create --subnet=192.168.4.0/24 final-rongjialin
```

再指定一下 IP：

```
# podman run -it --name client-rongjialin --network final-rongjialin --ip 192.168.4.205 centos /bin/bash
# podman run -it --name server-rongjialin --network final-rongjialin --ip 192.168.4.5 centos /bin/bash
```

很好，很有精神，我很满意（雾）！

互相 PING 测试：

```
# ping -c 3 ping -c 3 192.168.4.205   # 服务端执行
# ping -c 3 ping -c 3 192.168.4.5     # 客户端执行	
```

接下来是一些很无聊的工作，为了节省时间，继续 Tilix 开挂大法，开了同步输入。

看一下拉下来的是 CentOS Linux 8 还是 CentOS Stream 8（捂脸）。

```
# cat /etc/os-release
```

**干！是[今年年底就结束维护彻底凉掉](https://blog.centos.org/2020/12/future-is-centos-stream/)的 CentOS Linux 8，算了算了问题不大继续继续。**

参考[自己写的坑逼玩意](https://mirrors.ustc.edu.cn/help/contributor.html)（划掉）[USTC Mirror 文档](https://mirrors.ustc.edu.cn/help/centos.html)，把软件源换掉。

```
# sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org/$contentdir|baseurl=https://mirrors.ustc.edu.cn/centos|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-Linux-AppStream.repo \
         /etc/yum.repos.d/CentOS-Linux-BaseOS.repo \
         /etc/yum.repos.d/CentOS-Linux-Extras.repo \
         /etc/yum.repos.d/CentOS-Linux-PowerTools.repo \
         /etc/yum.repos.d/CentOS-Linux-Plus.repo
```

更新一下系统吧，Arch Linux 用户是真的不更新不舒服：

```
# dnf distro-sync
```
