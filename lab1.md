## 1. 实验一

好啦好啦切入正题不然就没分了，下面的命令在服务端执行，把包给装上了先。

```
# dnf search vsftpd    # 假装搜索一下这个包（?）
# dnf install vsftpd   # 这就装上
```

悲剧 +1...

```
# systemctl enable vsftpd --now
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```

**干！创建实例的时候忘记加 `--privileged` 了，用个鬼 `systemctl`。假装我刚才什么都没做好吧，重来重来（捂脸）。**

一定没有下次了.jpg

```
# podman run -id --privileged --name server-rongjialin --network final-rongjialin --ip 192.168.4.5 centos /usr/sbin/init
# podman exec -it server-rongjialin bash
```

整一个命令测试一下，不过加了之后还是好使的：

```
# systemctl list-units
```

好了，现在把上面的步骤重新走了一遍，就不贴图了。现在启动相应的服务：

```
# systemctl enable vsftpd --now
```

测试就使用 `ftp` 吧，在客户端安装 `ftp`：

```
# dnf install ftp
```

好像用 root 进行测试也不太好，各创建一个普通用户吧：

```
# useradd -m -g users -G wheel -s /bin/bash bobby285271
# passwd bobby285271
```

### 1.1 任务一

由于要允许匿名访问，改一下配置：

```
# vi /etc/vsftpd/vsftpd.conf
```

注释这行：

```
anonymous_enable=NO
```

重启一下服务：

```
# systemctl restart vsftpd
```

按要求创建一下文件：

```
# cd /var/ftp/pub
# vi anon.txt
```

在客户端登录 FTP，用 `anonymous` 登录（密码为空），查看文件。

```
$ ftp 192.168.4.5
> ls
```

### 1.2 任务二

在服务端家目录创建一个 `lisi` 文件夹，然后创建一个 `lisi.txt` 文件：

```
# cd /home/bobby285271/
# mkdir lisi
# cd lisi
# vi lisi.txt
```

在客户端登录 FTP，用 `bobby285271` 登录，查看文件。

```
$ ftp 192.168.4.5
> ls
> cd lisi
> ls
```

### 1.3 任务三

首先安装 `wget` ：

```
# dnf install wget
```

匿名下载 `anon.txt` 文件并验证：

```
$ wget ftp://192.168.4.5/pub/anon.txt
$ cat anon.txt
```

使用用户 `bobby285271` 下载 `lisi.txt`，这里就不直接贴出密码了，前面设了变量，然后 `clear` 清了屏：

```
$ wget --ftp-user=bobby285271 --ftp-password=${password} ftp://192.168.4.5/lisi/lisi.txt
$ cat lisi.txt
```
