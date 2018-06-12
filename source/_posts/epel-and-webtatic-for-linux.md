---
title: 启用和安装 EPEL & WEBTATIC 源
date: 2018-06-12 14:39:07
tags:
    - EPEL
    - WEBTATIC
    - yum
categories:
    - 系统环境
    - linux
---

@[TOC]

[EPEL][1](Extra Packages for Enterprise Linux, 企业版 Linux 附加软件包) 是一个 Fedora 特别兴趣小组，用以创建、维护以及管理针对企业版 Linux 的一个高质量附加软件包集，面向的对象包括但不限于红帽企业版 Linux (RHEL)、 CentOS、Scientific Linux (SL)、Oracle Linux (OL) 。
[Webtatic Yum repository][2]，是一个 CentOS/RHEL 软件存储库，其中包含较新的与 WEB 相关的软件包。

<!-- more -->

## 1 EPEL 特点
EPEL 的软件包通常不会与企业版 Linux 官方源中的软件包发生冲突，或者互相替换文件。EPEL 项目与 Fedora 基本一致，包含完整的构建系统、升级管理器、镜像管理器等等。另外，其在使用过程中，有以下特点：
- 不用去换原来 yum 源，安装后会产生新的 repo；
- EPEL 会有很多源地址，如果一个下载不成功，会去另外的地址下载；
- 更新时，如果下载的包不全，就不会进行安装，保证了依赖完整性。

## 2 EPEL 启用
```bash
# 查找软件名称
yum search epel
# 安装
yum install epel-release
```
![yum search epel](http://pic.hqmmw.com/markdown-img-paste-20180612154036867.png)
> 注：yum 搜索的结果中 `epel-release.noarch` 就是我们需要的软件包。安装可以不用包含软件包名称中 `.` 之后的部分，当然包含也没有问题。`.` 后的部分表示软件包适应系统构架。`noarch` 表示通用型；`x86_64` 表示 64 位系统；`i686` 表示 32 位系统。

## 3 查询 EPEL 包含内容
在 {% post_link centos-rpm CentOS rpm 包 %} 中介绍过 rpm 包相关内容及命令，这里可以使用 rpm 查询命令查看 EPEL 包含的内容。
```bash
rpm -ql epel-release
```
![quyer epel-release package](http://pic.hqmmw.com/markdown-img-paste-2018061216001662.png)

## 4 安装 WEBTATIC 源
对于服务器而言，最为常见的即是提供 web 服务，而对于 web 服务中常用的一些软件，在系统默认的源下，一般版本较低，如若需要使用相对较新的版本（如 PHP），WEBTATIC 源是一个很好的选择。
```bash
# CentOS/RHEL 7.x:
yum install epel-release
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

# CentOS/RHEL 6.x:
yum install epel-release
rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm
```


[1]: https://fedoraproject.org/wiki/EPEL/zh-cn
[2]: https://webtatic.com/projects/yum-repository/
