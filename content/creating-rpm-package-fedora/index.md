---
title: "Fedora 系发行版打包开源软件为.rpm格式详细教程"
date: 2022-10-21T14:55:25+08:00
draft: false
---
一步一步教你如何在fedora下将一个开源软件打包成rpm安装包

<!--more-->

## 首先安装好相关的打包工具

```bash
sudo dnf install rpmdevtools rpmlint
```

## 用rpmdev-setuptree命令生成打包的相关目录

```bash
rpmdev-setuptree
```

## 生成的目录如下: ~/rpmbuild/

BUILD  BUILDROOT  RPMS  SOURCES  SPECS  SRPMS

## 用rpmdev-newspec 快速生成一个hello.spec文件的模板

```bash
$ rpmdev-newspec hello
hello.spec created; type minimal, rpm version >= 4.17.
```

以一个开源的gnu hello 应用为示例，将刚刚生成的的hello.spec文件模板改成如下所示：

```bash
$ cat hello.spec
Name:           hello
Version:        2.10
Release:        1%{?dist}
Summary:        a test tool

License:        GPL
URL:            https://www.gnu.org/software/hello/
Source0:        https://ftp.gnu.org/gnu/hello/hello-2.10.tar.gz

BuildRequires:  gcc make patch
Requires:       glibc

%description
The GNU hello program

%prep
%autosetup


%build
%configure
%make_build


%install
%make_install


%files
#%license add-license-file-here
%doc README
%{_bindir}/hello
%{_datarootdir}/*



%changelog
* Mon Mar 14 2022 winq <winq2008@qq.com>
-package init
```

> 如果您看到一个不熟悉的宏，可以使用以下方法了解详细信息

```bash
$ rpm --eval %{_bindir}
/usr/bin
```

## 将这个spec问件拷贝到SPECS文件夹下

```bash
# 用命令检查一下刚写的spec文件可正确无误
rpmlint ./hello.spec
```

```bash
# Spectool是一个从spec文件中的源码地址下载源代码和补丁的工具。
spectool -g -R ~/rpmbuild/SPECS/hello.spec
```

用rpmbuild命令生成rpm
-ba: 参数表示依据 specfile 构建源代码和二进制包

```bash
rpmbuild -ba hello.spec
```
