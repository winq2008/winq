---
title: "视音频格式转换so easy！"
date: 2022-02-17T07:57:55+08:00
draft: false
---
说到视频、音频格式转换这个问题，以前在windows平台下，百度，google各种寻找转换软件。
一个个试下来，不是有的打开很慢，有的只能转换指定的几种格式，转换速度不理想，
支持格式不全面。

<!--more-->

自从转到linux平台下，发现这事变得简单了。
也许是以前了解得不多，也听说过有一个FFmpega开源项目是做视频播放的音视频解码器。
很多播放器都采用这种FFmpega。好奇心的驱使，闲下来的时候打开FFmpega的官方网站瞄了
一眼。这不打紧，主页一名话，引起了我的兴趣。

## Converting video and audio has never been so easy

```bash
$ffmpeg -i input.mp4 output.avi
```

各位看官，看到这里，估计已经明白了，ffmpeg可以做视音频转换的活。
没错，so easy. 就是这么简单。

> [FFmpega官方网站](https://ffmpeg.org/)

那还有人可能要问，这玩意怎么安装下啊？

在fedora发行版下：

```bash
dnf install ffmpeg
```

其他发行版，可用类似的发行版包管理器安装，或可自行看相关发行版安装说明 或百度 或google。
