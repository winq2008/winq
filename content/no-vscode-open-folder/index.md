---
title: "解决安装vscode后,改变了其它应用每次打开文件夹时运行vscode的诡异行为"
date: 2022-02-24T15:35:01+08:00
draft: false
---

安装了visual code 以后，从其它应用中打开文件目录时程序变成了运行vscode，而不是打开对应的文件夹?
遇到这个问题怎么办呢？

<!--more-->
别急！请看以下解决方案：

用vim打开文件

```bash
$vim ~/.config/mimeapps.list
```

在文件中搜索inode/directory关键字,找到这一行

```bash
inode/directory=nde-fileman.desktop
```

然后进入以下目录

```bash
cd  /usr/share/applications 
```

创建nde-fileman.desktop文件

```bash
sudo vim nde-fileman.desktop
```

输入以下内容：

```bash
[Desktop Entry]
Name=open Folder
Comment=open Folder
Exec=/usr/bin/pcmanfm
Type=Application
StartupNotify=false
MimeType=inode/directory;
```

到这里问题就解决了。

以上在fedora 35下设置测试通过。其它发行版没有经过测试,仅供参考。
