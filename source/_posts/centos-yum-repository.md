---
title: Centos yum 源
date: 2018-06-12 17:12:08
tags:
    - centos
    - yum
    - php
    - nginx
categories:
    - 系统环境
    - centos
---

@[TOC]

在日常的 linux 使用过程中，安装软件是最为常见的一种操作，而软件的来源，对于 centos 来说，常用的软件工具则是 yum ，本文收集我们在使用过程中，常用的 yum 源。

<!-- more -->

## 1 EPEL 源 & WEBTATIC 源
在 {% post_link epel-and-webtatic-for-linux 启用和安装 EPEL & WEBTATIC 源 %} 一文中，详细介绍我解释了该两种源的安装方法，其中 EPEL 源的启用，是很多其他源安装的基础。
其中 WEBTATIC 源中，包含有我们 WEB 服务中常用的软件，如：PHP等。

## 2 Nginx 源
Nginx 是 web 开发过程中，最为常见的一种服务软件，因此，添加相应的源，并安装成了日常 web 服务运维过程中最为常见的操作。
进入 [Nginx][1] 官网，点击右侧的 [download][2] 链接，进入下载页面，找到页面中 `Pre-Built Packages` 项，选择其中的 [mainline version][3] ，进入到相应的说明页面。
针对于 RHEL/CentOS 系列 Linux ，我们新建文件 `/etc/yum.repos.d/nginx.repo`，配置内容如下：
```ini
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/OS/OSRELEASE/$basearch/
gpgcheck=0
enabled=1
```
> 注：根据系统类型，将 `OS` 替换为对应值：`rhel` 或 `centos` ；其中 `OSRELEASE` 依据系统的实际版本，替换为：`6` 或 `7` ，分别针对于 6.x 或 7.x 系列的系统。
![config yum repo for nginx](http://pic.hqmmw.com/markdown-img-paste-20180612220835880.png)

## 3 Mysql 源
系统本身包含 mysql ，但其版本相对较低，为了能使用较新的版本，我们可以使用官方提供的源进行安装。
进入 [官方获取 yum 源][4]，查看系统版本，下载对应的安装文件。
```bash
# 如本机为 centos 7.x
wget https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
# 安装源
rpm -ivh mysql80-community-release-el7-1.noarch.rpm
```

## 4 REMI 源
[Remi][5] 源，软件几乎都是最新稳定版，其主要贡献成员都是一群 Linux 骨灰级玩家编译好放入源里的，对于系统环境和软件编译参数的熟悉程度毋庸置疑。
添加 Remi 源：进入其官网，可以选择对应地区的源，如本例中选择 `CN` ，由此而确定源地址为：https://mirrors.tuna.tsinghua.edu.cn/remi ，再根据系统版本，执行如下命令。
```bash
yum install epel-release

# CentOS 6.x
rpm -ivh https://mirrors.tuna.tsinghua.edu.cn/remi/enterprise/remi-release-6.rpm

# CentOS 7.x
rpm -ivh https://mirrors.tuna.tsinghua.edu.cn/remi/enterprise/remi-release-7.rpm
```
> 注：默认情况下，Remi 源没有启用，我们需要手动启动相应源。打开 `/etc/yum.repos.d/remi.repo` 文件，将容器 `[remi]` 下的 `enabled=0` 修改为 `enabled=1`，其他容器下的不用修改。（相关参数的详细说明可参考 {% post_link centos-yum-repo-admin CentOS 源 & yum 管理命令 %}）

## 5 CODEIT 源
[Codeit][6] 是一个软件公司，其制作发布了一个相关软件的源，主要是针对 CentOS 。其特点是，包含有较新版本的 httpd、nginx 等。
```bash
# 依赖 epel
yum install epel-release

# 自动判断系统版本，并下载对应源
# 亦可自行进入目录，手动下载对应版本源
cd /etc/yum.repos.d && wget https://repo.codeit.guru/codeit.el`rpm -q --qf "%{VERSION}" $(rpm -q --whatprovides redhat-release)`.repo
```

[1]: http://nginx.org/
[2]: http://nginx.org/en/download.html
[3]: http://nginx.org/en/linux_packages.html#mainline
[4]: https://dev.mysql.com/downloads/repo/yum/
[5]: http://rpms.famillecollet.com/
[6]: https://codeit.guru/en_US/
