---
title: "linux系统下更改应用程序与不同文件类型的默认关联"
date: 2022-03-15T18:25:59+08:00
draft: false
---

想必大家在windows系统下，都知道如果系统同时安装了两个浏览器
比如`firefox`或`Microsoft Edge`。当同时有二个浏览器，你必须有一个
是黙认浏览器。当你用鼠标双击直接打开个一个比如扩展名为`.html`类型的文件时，
这时系统就会调用你的默认浏览器打开。
<!--more-->

那么你转到linux系统下，出现同样的问题。两个浏览器，从浏览器设置(firefox或Microsoft Edge)中将其设置为默认。
当你从其它应用打开一个链接时，可能会发现一个很诡异的行为，调出来的浏览器并不是你设置的默认的浏览器。
why?

要解决这个问题，你不得不简单了解一下mime类型

MIME(Multipurpose Internet Mail Extensions)多用途互联网邮件扩展类型。是设定某种扩展名的文件用一种应用程序来打开的方式类型，当该扩展名文件被访问的时候，浏览器会自动使用指定应用程序来打开。多用于指定一些客户端自定义的文件名，以及一些媒体文件打开方式。

> 以上描述来自百度百科

在linux下mime类型是由一个xml格式的文件来描述的
mime类型描述文件一般都存在于以下的系统路径下：

```bash
/usr/share/mime
~/.local/share/mime
~/.config/
```

下面说一下，怎么查询一个具体文件的mime类型是什么呢？
示例如下：

```bash
$ xdg-mime query filetype b.txt 
text/plain

$ dg-mime query filetype 前言.md 
text/markdown
```

如上，既然能查询出具体文件的mime类型了。
下面我们就可以通过mime类型进一步查询出与这种类型相关联的应用程序。
示例如下：

```bash
plain text 格式的文件 默认是用leafpad 打开的。
$ xdg-mime query default text/plain
leafpad.desktop     # 说明:这是一个应用程序桌面描述文件
```

知道了一个具体文件默认打开的应用程序后，我们怎么将这个默认的。改为其它应用程序呢？

> 说明：mimeapps.list 用于描述mime类型默认关联的应用程序。其中系统中的关联信息都在里面。也可以从这个文件中查询关联关系。

mimeapps.list存在于以下的两个路径:

```bash
~/.config/mimeapps.list
/usr/share/applications/mimeapps.list
~/.local/share/applications/mimeapps.list
```

下面通一个实例来说明一下

```bash
$ cat /usr/share/applications/mimeapps.list| grep html
text/html=firefox.desktop
application/xhtml+xml=firefox.desktop
```

或者通过如下命令查询

```bash
xdg-settings get default-web-browser
或
xdg-mime query default x-scheme-handler/http
xdg-mime query default x-scheme-handler/https
```

能过上面的查询我们知道系统中默认打开html、xhmtl、xml的应用都是firefox

下面我们将默认浏览器改为qutebrowser

```bash
xdg-mime default  org.qutebrowser.qutebrowser.desktop x-scheme-handler/http
xdg-mime default  org.qutebrowser.qutebrowser.desktop x-scheme-handler/https
```

更新 mimeapps.list 和  mimeinfo.cache

```bash
update-desktop-database  ~/.config/
```

最后再用如下命令查询确认更改默认浏览器是否成功

```bash
$ xdg-settings get default-web-browser
org.qutebrowser.qutebrowser.desktop
```

通过如下命令可以详细了解系统中的浏览器的与mime情况

```bash
gio mime x-scheme-handler/https
用于“x-scheme-handler/https”的默认应用程序：org.qutebrowser.qutebrowser.desktop
已注册的应用程序：
    firefox.desktop
    microsoft-edge.desktop
    org.qutebrowser.qutebrowser.desktop
推荐的应用程序：
    firefox.desktop
    microsoft-edge.desktop
    org.qutebrowser.qutebrowser.desktop
```

