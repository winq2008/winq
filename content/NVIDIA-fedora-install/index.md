---
title: "Fedora 安装NVIDIA的闭源显卡驱动详细教程"
date: 2022-12-12T08:37:13+08:00
draft: false
---

linux 用户对nvidia的显卡的驱动感情是复杂的。本人有一台笔记本就是N卡，一直用开源的驱动，最近遇到了一些显示问题，所以痛下决心，好好
看看这方面的资料，安装闭源驱动试试，所以有了这么一篇总结性的文章。如果有其它同样问题的小伙伴可以做个参考！
<!--more-->

安装NVIDIA驱动程序

## 在开始安装之前要做的事

开始安装之前，请退出X服务器并终止所有OpenGL应用程序（请注意，即使在X服务器停止后，某些OpenGL应用程序仍可能存在）。您还应该在系统上设置默认运行级别，使其能够引导到VGA控制台，而不是直接引导到X。这样做可以在安装过程中出现问题时更容易恢复。

开始安装之前，请退出X服务器并终止所有OpenGL应用程序（请注意，即使在X服务器停止后，某些OpenGL应用程序仍可能存在）。您还应该在系统上设置默认运行级别，使其能够引导到VGA控制台，而不是直接引导到X。这样做可以在安装过程中出现问题时更容易恢复。

## 什么是Nouveau，为什么我需要禁用它？

Nouveau是NVIDIA GPU的显示驱动程序，通过NVIDIA驱动程序的逆向工程作为开源项目开发。它作为NVIDIA硬件的默认显示驱动程序随许多当前的Linux发行版一起提供。它不受NVIDIA开发或支持，也与NVIDIA驱动程序无关，除了Nouveau和NVIDIA驱动器都能够驱动NVIDIA GPU这一事实。一次只能有一个驱动程序控制GPU，因此，如果GPU由Nouveau驱动程序驱动，则必须在安装NVIDIA驱动程序之前禁用Nouveau。
Nouveau在内核中执行模式集。这可能会使禁用Nouveau变得困难，因为内核模式集用于显示帧缓冲控制台，这意味着即使X未运行，Nouveau也将被使用。只要Nouveau正在使用，其内核模块就无法卸载，这将阻止NVIDIA内核模块加载。因此，在安装NVIDIA驱动程序之前，务必确保Nouveau的内核模式设置已禁用。

## 如何防止Nouveau加载和执行内核模式集？

防止Nouveau加载和执行内核模式集的一个简单方法是将模块加载器的配置指令添加到系统模块加载器配置目录之一的文件中：例如/etc/modprob.d/或/usr/local/modprobe.d。从技术上讲，这些配置指令可以添加到这些目录中的任何文件中，但这些目录中许多现有文件都由您的发行版提供和维护，发行版可能会不时提供可能与您的更改相冲突的更新配置文件。因此，建议创建一个新文件，例如/etc/modprobe.d/disable-nouveau.conf，而不是编辑一个现有文件，例如流行的/etc/modprobe.d/blacklist.conf。请注意，一些模块加载器只会在名称以.conf结尾的文件中查找配置指令，因此如果要创建新文件，请确保其名称以.conf结束。
无论您选择创建新文件还是编辑现有文件，都需要添加以下两行：

```bash
blacklist nouveau
options nouveau modeset=0
```

第一行将阻止Nouveau的内核模块在启动时自动加载。它不会阻止手动加载模块，也不会阻止X服务器加载内核模块；请参阅“如何防止X服务器加载Nouveau？”在下面第二行将阻止Nouveau执行内核模式集。如果没有内核模式集，则可以卸载Nouveau的内核模块，以防意外或故意加载。

添加这些配置指令后，您需要重新启动系统才能使其生效。
如果nvidia安装程序检测到系统正在使用Nouveau，它将提供创建这样一个modprobe配置文件来禁用Nouveau。

## 如何防止X服务器加载Nouveau？

将Nouveau列入黑名单只会阻止它在启动时自动加载。如果X服务器作为正常启动过程的一部分启动，并且该X服务器使用Nouveau X驱动程序，那么Nouveau内核模块仍将被加载。如果发生这种情况，您可以在停止X服务器后使用“modprobe-r Nouveau”卸载Nouveau，只要您注意防止它执行内核模式集；然而，最好先确保X不加载新产品。
如果您的系统未配置为在启动时启动X服务器，则只需在重新启动后运行NVIDIA驱动程序安装程序。否则，最简单的方法就是编辑X服务器的配置文件，以便X服务器使用与卡兼容的非模式设置驱动程序，例如vesa驱动程序。然后，您可以停止X并照常安装驱动程序。请查阅X服务器的文档以确定X服务器配置文件的位置。

## 启动安装程序

下载文件NVIDIA-Linux-x86_64-340.108.run后，转到包含下载文件的目录，并作为root用户运行可执行文件：

```bash
# cd yourdirectory
# sh NVIDIA-Linux-x86_64-340.108.run
```

run文件是一个自解压的归档文件。当执行时，它会提取归档文件的内容并运行包含的nvidia安装程序实用程序，该实用程序提供了一个交互式界面来指导您完成安装。
nvidia安装程序还将自己安装到/usr/bin/nvidia中，稍后可以使用该安装程序卸载驱动程序、自动下载更新的驱动程序等。
您还可以向.run文件提供命令行选项。下面列出了一些更常见的选项。
Common .run Options
--info
    打印.run文件的嵌入信息并退出。
--check
    检查存档的完整性并退出。
--extract-only
    提取./NVIDIA-Linux-x86_64-340.108.run的内容，但不运行NVIDIA安装程序。
--help
    打印常用命令行选项的使用信息并退出。
--advanced-options
   打印常用命令行选项和高级选项的使用信息，然后退出。

## Installing the Kernel Interface

NVIDIA内核模块有一个内核接口层，必须为每个内核专门编译。NVIDIA将源代码分发到此内核接口层。
当安装程序运行时，它将检查系统中所需的内核源并编译内核接口。您必须安装内核的源代码才能进行编译。在大多数系统上，这意味着您需要找到并安装正确的内核源、内核头或内核开发包；在某些发行版上，不需要额外的包。
编译正确的内核接口后，内核接口将与NVIDIA内核模块的封闭源代码部分链接。这需要在系统上安装链接器。链接器（通常为/usr/bin/ld）是binutils包的一部分。在安装NVIDIA驱动程序之前，必须先安装链接器。

## Registering the NVIDIA Kernel Module with DKMS

安装程序将检查系统上是否存在DKMS。如果找到DKMS，您将可以选择向DKMS注册内核模块，并使用DKMS基础架构构建和安装内核模块。在大多数使用DKMS的系统上，当安装不同的Linux内核时，DKMS会自动重建已注册的内核模块。
如果nvidia安装程序无法通过DKMS安装内核模块，则安装将中止，并且不会安装内核模块。如果发生这种情况，应再次尝试安装，而不使用DKMS选项
请注意，在版本304之前随驱动程序一起提供的nvidia安装程序版本不与DKMS交互。如果您选择在DKMS中注册NVIDIA内核模块，请确保在使用非DKMS感知版本的NVIDIA安装程序安装较旧的驱动程序之前，从DKMS数据库中删除该模块；否则，可以删除模块源文件而不首先注销模块，从而可能使DKMS数据库处于不一致状态。在使用较旧的安装程序安装驱动程序之前运行nvidia卸载将调用正确的dkms remove命令来清理安装。

## 安装程序的其他功能

如果没有选项，.run文件将在解压缩后执行安装程序。安装程序可以作为过程中的一个单独步骤运行，也可以稍后运行以获取更新等。nvidia安装程序的一些更重要的命令行选项包括：
nvidia-installer options
--uninstall
    在安装过程中，安装程序将备份任何冲突文件并记录新文件的安装。卸载选项撤消安装，将系统恢复到安装前的状态。
--ui=none
如果安装程序能够找到正确的ncurses库，则使用基于ncurses的用户界面。否则，它将返回到一个简单的命令行用户界面。此选项禁用ncurses库的使用。

## 为NVIDIA驱动程序配置X

NVIDIA驱动程序包括一个名为NVIDIA-xconfig的实用程序，该实用程序旨在使编辑X配置文件变得容易。您也可以手动编辑它。

### 使用nvidia-xconfig配置X服务器

nvidia-xconfig将找到X配置文件并将其修改为使用nvidia X驱动程序。在大多数情况下，当安装程序询问是否应该运行它时，您可以简单地回答“是”。如果以后需要重新配置X服务器，可以从终端再次运行nvidia-xconfig。nvidia-xconfig将在修改配置文件之前对其进行备份。
请注意，必须重新启动X服务器，对其配置文件的任何更改才能生效。
通过运行，可以在nvidia-xconfig手册页中找到有关nvidia_xconfig的更多信息。

```bash
% man nvidia-xconfig
```

### 手动编辑配置文件

2004年4月，X.OrgFoundation发布了一个基于XFree86服务器的X服务器。虽然您的版本可能使用X.Org X服务器，而不是XFree86，但两者之间的差异对NVIDIA Linux用户没有影响，但有两个例外：

* X.Org配置文件是/etc/X11/xorg.conf，而XFree86配置文件是/etc/X11/XF86Config。这些文件使用相同的语法。本文档将这两个文件称为“X配置文件”。
* X.Org日志文件为/var/log/Xorg.#.log，而XFree86日志文件是/var/log/XFree86.#。log（其中#是服务器号，通常为0）。日志文件的格式几乎相同。本文档将这两个文件称为“X日志文件”。
为了将任何更改读入X服务器，必须编辑服务器使用的文件。虽然简单地编辑两个文件并不是不合理的，但通过搜索行可以很容易地确定正确的文件
 (==) Using config file:
在X日志文件中。该行指示正在使用的X配置文件的名称。
如果您没有一个有效的X配置文件，有几种不同的方法可以获得一个。XFree86发行版和NVIDIA驱动程序包中都包含一个示例配置文件（位于/usr/share/doc/NVIDIA_GLX-1.0/）。nvidia驱动程序包提供的nvidia-xconfig实用程序可以生成新的X配置文件。有关X配置语法的其他信息可以在XF86Config手册页（man XF86Config或man xorg.conf）中找到。
如果您有一个不同驱动程序（如“nv”或“vesa”驱动程序）的X配置文件，那么只需按如下方式编辑该文件。
Remove the line:

```bash
      Driver "nv"
  (or Driver "vesa")
  (or Driver "fbdev")
```

并将其替换为以下行：

```bash  
Driver "nvidia"
```

删除以下行：

```bash
   Load "dri"
   Load "GLCore"
```

文件的“模块”部分中，添加行（如果不存在）：

```bash
Load "glx"
```

如果X配置文件没有Module部分，如果系统上安装的X服务器是X.Org X服务器或XFree86 X版本4.4.0或更高版本，则可以安全地跳过最后一步。如果您使用的是较旧的XFree86 X服务器，请将以下内容添加到X配置文件中：
```bash
Section "Module"
    Load "extmod"
    Load "dbe"
    Load "type1"
    Load "freetype"
    Load "glx"
EndSection
```
可以向X配置文件中添加许多选项来调整NVIDIA X驱动程序。 <http://us.download.nvidia.cn/XFree86/Linux-x86_64/340.108/README/xconfigoptions.html>
完成对X配置文件的这些编辑后，可以重新启动X并开始使用加速OpenGL库。重新启动X后，任何OpenGL应用程序都应自动使用新的NVIDIA库。

## 卸载驱动程序后恢复X配置

如果X被明确配置为使用NVIDIA驱动程序，则卸载NVIDIA驱动器后，应编辑X配置文件以使用不同的X驱动程序。否则，X可能无法启动，因为在卸载后，它配置使用的驱动程序将不再存在于系统中。
如果手动编辑了文件，请还原所做的任何编辑。如果您使用了nvidia-xconfig实用程序，在安装程序提示您配置X服务器时回答“是”，或者稍后手动运行该实用程序，则可以恢复备份的X配置文件（如果该文件存在并且反映了安装nvidia驱动程序之前存在的X配置状态）。
如果您不记得对该文件所做的任何手动更改，或者没有使用非NVIDIA X驱动程序的备份X配置文件，您可能需要尝试简单地重命名X配置文件以查看X服务器是否加载了合理的默认值。
