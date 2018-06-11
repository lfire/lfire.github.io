---
title: Docker 和 vmware 共存工作
date: 2018-06-09 07:26:55
tags:
    - Docker
    - vmware
    - Hyper-V
categories:
    - 系统环境
---

@[TOC]

<!-- more -->

## 1 起因

在之前装好系统环境后，想要在工作平台上模拟服务器的环境，来进行一下相关的实验，因此，系统上装好有 vmware 软件，该软件下的快照功能，让我对于相关的实验环境有一个很好的备份操作，很是 nice。
而另一个方面，当前 docker 的活跃程度，也让我这个技术屌丝，内心有点点萌动，想了解一下，如果可以玩出点花样，那也是极好的。
但是，当我尝试了在 windows 环境下，安装好了 docker 后，提示要开启 Hyper-V ，开启后，正常启动 docker 服务。（`控制面板->程序->程序和功能->启用或关闭 Windows 功能`）
![start Hyper-V](http://pic.hqmmw.com/markdown-img-paste-20180611095532509.png)
某天的某天，再次捡起 vmware 时，确报出了错误。
![vmware 报错](http://pic.hqmmw.com/Snipaste_2018-06-08_21-50-16.png)
后面查阅了相关资料，原来，Hyper-V 与 vmware 有冲突，只能二选一。

## 2 解决方案

当需要使用 vmware 时，我们按开启的方式，再次关闭 Hyper-V ，这样，软件就能正常的打开了。
但这样操作，有一个不好的地方，每开启或关闭一次，系统都必需要重启一次，这导致每次系统都进行了一次程序的安装和卸载，费时不高效。

## 3 更好的办法

建立两个启动项，一个开启了 Hyper-V，而另一个则关闭，这样，我们可以在需要该功能时，在系统启动界面自由的选择相应的启动项，而不用频繁的安装和卸载 Hyper-V。
建立新的启动项，并将 Hyper-V 功能关闭，命令如下：

```bash
# win + X 开启命令行，注意：必须以管理员身份执行
bcdedit /copy {default} /d "Windows 10 Without Hyper-V"
# 以上命令得到输出 {xxxxxxxxxxxxxxxxxxx}
bcdedit /set {xxxxxxxxxxxxxxxxxxx} hypervisorlaunchtype off
```

如图：
![bcdedit](http://pic.hqmmw.com/Snipaste_2018-06-08_17-21-01.png)

我们可以通过命令：`bcdedit /enum`，查看启动项列表。
![get bcdedit list](http://pic.hqmmw.com/markdown-img-paste-20180611101908890.png)

## 4 bcdedit 简单用法

以上的操作中，用到了一个很重要的命令：`bcdedit`，其主要功能是建立和重新配置 bootloader。（Boot Configuration Data Edit）
有一些常用的用法：

```bash
# 查看帮助
bcdedit /?

# 查看启动项列表
bcdedit /enum
# or 查看所有
bcdedit /enum all

# 设置某个启动项配置值
bcdedit /set {xxxx} description "Windows 10 With Hyper-V"
# or
bcdedit /set {xxxx} hypervisorlaunchtype on

# 设置启动项显示排列顺序
bcdedit /displayorder {current} {xxxxx} {xxxxxx}

# 创建新的启动项
bcdedit /create /d "A New One"

# 复制启动项
bcdedit /copy {xxxx} /d "A Copy One"

# 删除启动项
bcdedit /delete {xxxx}
# or 彻底删除
bcdedit /delete /cleanup {xxxx}

# 设置默认启动项
bcdedit /default {xxxxx}

# 设置默认的启动菜单显示时间，单位秒
bcdedit /timeout 10
```
