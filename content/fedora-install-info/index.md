---
title: "安装Fedora后要做的一些相关设置 "
date: 2022-11-10T18:33:27+08:00
draft: false
---
安装 Fedora 后有必要做的一些设置,在此记录一下
<!--more-->

1 设置软件源
Fedora 默认使用 Metalink 给出推荐的镜像列表，保证用户使用的镜像仓库足够新，并且能够尽快收到安全更新，从而提供更好的安全性。所以通常情况下使用默认配置即可，无需更改配置文件。

2 更新系统

```bash
sudo dnf update
```

3 启用 RPM Fusion 软件源
RPM Fusion 官方网址<https://rpmfusion.org/>

4  添加 Flathub 存储库
Fedora 默认情况下启用了 Flatpak。 但是，它是过滤后的 Flatpak
因此，要访问各种可用的 Flatpak 应用程序，你可以在终端中使用以下命令添加 Flathub 存储库：

```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

5 配置 DNF 以更快地下载包
Fedora 可以通过多种方法增强下载包的速度。比如选择最快的镜像，可以提高包下载速度。此外，如果你的互联网连接速度足够快，则可以更改并行下载的数量以获得更快的下载。
要做这两件事，只需编辑位于
/etc/dnf/dnf.conf

```bash
fastestmirror=true
deltarpm=true
max_parellel_downloads=10
```

fastestmirror  为选择最快软件源，如果你手动修改了仓库里面的信息则不需要启动这个
deltarpm  相当于增量下载，把软件增加的部分下载下来，和原软件包合成新软件包，类似于现在的 Android 软件更新
max_parellel_downloads 设置最大并行下载数量

6  配置 NTP 以获得准确的时间
网络时间协议（NTP）是用来使计算机时间同步化的一种协议，它可以使计算机对其服务器或时钟源做同步化，它可以提供高精准度的时间校正。
Fedora 默认使用 chrony 来进行时间同步。
可以修改
/etc/chrony.conf
将 pool 的值选择为下列中的其中一个即可:

```bash
# 中国 NTP 授时快速服务
pool cn.ntp.org.cn 
# 阿里云 NTP
pool ntp.aliyun.com 
# 腾讯云 NTP
pool ntp.tencent.com 
```

随后重启 chrony 即可。

```bash
sudo systemctl restart chronyd.service
```

7  配置家目录下默认文件夹的名字

使用如下命令

```bash
xdg-user-dirs-update
```

使用 LC_ALL=C xdg-user-dirs-update --force 命令可以强制创建英语目录
