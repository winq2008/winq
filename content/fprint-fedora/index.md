---
title: "Fedora系统下启用指纹扫描功能"
date: 2022-12-05T20:21:37+08:00
draft: false
---
一直以来,笔记本都有一个指纹接口,以前一直习惯用密码登录,
也没有考虑过在fedora下怎么用指纹登录,想着让它闲着不如试用一下。
结果是用着也还行,现将用法在此做个记录。
<!--more-->

## 前提条件是你的硬件得支持指纹扫描

首先需要您的设备上有兼容的指纹识别设备， 可以查看兼容设备清单来确定是否支持您的指纹识别设备。
Supported Devices: <https://fprint.freedesktop.org/supported-devices.html>
使用下面命令查看您的指纹设备信息:

```bash
lsusb
```

## 系统上得有软件包 fprintd,没有的话，就安装一个

```bash
dnf install  fprintd
```

## 使用以下命令添加指定手指 指纹,一共要求扫描五次

```bash
sudo fprintd-enroll 用户名
# 命令执行后提示如下与扫描指纹反馈如下
Using device /net/reactivated/Fprint/Device/0
Enrolling right-index-finger finger.
Enroll result: enroll-stage-passed
Enroll result: enroll-stage-passed
Enroll result: enroll-stage-passed
Enroll result: enroll-stage-passed
Enroll result: enroll-stage-passed
Enroll result: enroll-completed
```

## 列出本机指定用户下已注测的指纹信息

```bash
sudo fprintd-list winq
```

## 验证您本机刚注册过的指纹信息是否正确

```bash
sudo fprintd-verify winq
# 命令执行后提示如下信息,表示指纹信息验证通过
Using device /net/reactivated/Fprint/Device/0
Listing enrolled fingers:
 - #0: right-index-finger
Verify started!
Verifying: right-index-finger
Verify result: verify-match (done)
```
