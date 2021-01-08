## 2. 实验二

Arch Linux 用户习惯性翻 [PKGBUILD](https://github.com/archlinux/svntogit-packages/blob/packages/apache/trunk/PKGBUILD) 去了，突然意识到不对劲，这回是 CentOS 啊...

从北外镜像站下载了 HTTPD 的源码包，解压：

```
# wget https://mirrors.bfsu.edu.cn/apache//httpd/httpd-2.4.46.tar.gz
# gzip -d httpd-2.4.46.tar.gz
# tar xvf httpd-2.4.46.tar
```

查看安装指引：

```
# cd httpd-2.4.46
# cat INSTALL | more
```

是经典的编译三部曲。直接开跑，根据报错安装各种依赖项：

```
# ./configure --prefix=/usr/local/apache2
```

首先是 APR：

```
checking for APR... no
configure: error: APR not found.  Please read the documentation.
```

```
# dnf install apr apr-devel apr-util apr-util-devel
```

接下来是 GCC：

```
checking whether the C compiler works... no
configure: error: in `/root/lab2/httpd-2.4.46':
configure: error: C compiler cannot create executables
See `config.log' for more details
```

```
# dnf install gcc
```

接下来是 PCRE：

```
checking for pcre-config... false
configure: error: pcre-config for libpcre not found. PCRE is required and available from http://pcre.org/
```

```
# dnf install pcre pcre-devel
```

不错不错，似乎没别的问题了。

接下来来到第二步，先把 `make` 装上：

``` 
# dnf install make
```

然后开始编译：

```
# make
```

提示 `/usr/lib/rpm/redhat/redhat-hardened-ld` 不存在，安装 `redhat-rpm-config`，继续 `make`。

```
gcc: error: /usr/lib/rpm/redhat/redhat-hardened-ld: No such file or directory
```

```
# dnf install redhat-rpm-config
```

似乎很顺利了。


接下来最后一步：

```
# make install
```

### 2.1 任务一

这里略过 PATH 的配置，进入安装目标路径：

```
# cd /usr/local/apache2
```


在服务端启动 HTTPD：

```
# ./bin/apachectl -k start
```

同时，为了测试访问效果，在客户端安装浏览器。首先启用 PowerTools 仓库：

```
# vi /etc/yum.repos.d/CentOS-Linux-PowerTools.repo
# dnf makecache
```


然后安装 Lynx：

```
# dnf install lynx
```


打开浏览器：

```
# lynx
```


按下 `G` 编辑地址 `http://192.168.4.5/`，回车访问。

就这？
