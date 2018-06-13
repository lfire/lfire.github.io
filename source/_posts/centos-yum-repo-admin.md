---
title: Centos 源 & yum 管理命令
date: 2018-06-11 16:10:32
tags:
    - centos
    - yum
    - 源管理
categories:
    - 系统环境
    - linux
---

@[TOC]

<!-- more -->

## 1 yum 源

源，是 linux 系统中，用于管理相关软件来源的一个配置，而 yum 源，则是 red 系列的一个管理特例。
源的目录位置：`/etc/yum.repo.d/`
![yum repo files](http://pic.hqmmw.com/markdown-img-paste-20180611163911778.png)
如上图中，如果系统能与外网相通，则使用默认的网络 yum 源文件 `CentOS-Base.repo`；若只能内网通信，则使用光盘 yum 源文件 `CentOS-Media.repo`。
源文件配置说明：
| 参数 | 说明 |
|:-----|:-----|
| `[base]` | 容器名称，一定要放在 `[]` 中 |
| `mirrorlist` | 镜像站点，用这个地址或下面那个地址都行 |
| `baseurl` | yum 源服务器地址。默认是 CentOS 官方的 yum 源服务器，是可以使用的，如若访问速度很慢，可以改为合适的地址。 |
| `enabled` | 此容器是否生效，如果不配置，或配置为 `enabled=1` 表示生效；配置为 `enabled=0` 表示不生效。 |
| `gpgcheck` | 配置 RPM 数字证书是否生效， `gpgcheck=1`：代表生效； `gpgcheck=0`：代表不生效。 |
| `gpgkey` | 数字证书的公钥文件保存位置，一般不用修改。 |

## 2 网络源
```bash
# 查看网络源配置
vim /etc/yum.repos.d/CentOS-Base.repo
```
![centos yum config](http://pic.hqmmw.com/markdown-img-paste-20180612091852727.png)

## 3 光盘源
当处于离线状态时，我们需要从其他源来安装软件，如：光盘。
相关的步骤如下：
* 挂载光盘
```bash
# 新建光盘挂载点
mkdir /mnt/cdrom

# 把设备文件名挂载到挂载点
mount /dev/cdrom /mnt/cdrom
```

* 让网络 yum 源失效
```bash
# 进入 yum 源配置文件夹
cd /etc/yum.repos.d/

# 将 yum 网络源备份
mv CentOS-Base.repo CentOS-Base.repo.bak
```
> 注：网络 yum 源失效后，系统默认使用光盘 yum 源。

* 修改光盘 yum 源
```bash
vim CentOS-Media.repo
```
```ini
[c7-media]
name=CentOS-$releasever - Media
baseurl=file:///mnt/cdrom
# file:///media/cdrom/
# file:///media/cdrecorder/
# 注释这两个不存在的地址
gpgcheck=1
enabled=1
# 把 enabled=0 改为1，让这个yum源配置文件生效
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

## 4 yum 命令
在 linux 日常使用过程中， yum 操作经常会涉及到，以下对常用的方式进行总结：
* 查询
```bash
# 在远程服务器上查询所有可用的软件包列表
yum list

# 显示本地安装好的软件列表
yum list installed

# 搜索远程服务器上所有和关键字相关的包
yum search [keywords]

# 查看软件包信息
yum info [package-name]
```

* 安装
```bash
yum -y install [package-name]
# 选项：
#       install     安装
#       -y          自动回复 yes
```

* 升级
```bash
# 升级某个软件包
yum -y update [package-name]
# 选项：
#       update      升级
#       -y          自动回复 yes

# 升级所有包，同时升级软件和系统内核
yum -y update

# 升级所有包，但不升级软件和系统内核
yum -y upgrade
```

* 卸载
```bash
yum -y remove [package-name]
# 选项：
#       remove      卸载
#       -y          自动回复 yes
```

* 添加源后更新
```bash
yum clean all
yum update
```

5 yum 源优先级
如若喜欢优先从某个源安装软件，建议使用 `yum-priorities` 插件，该插件的作用是给多个源排定优先级，当多个源中存在同一软件的时候，软件会优先使用优先级高的源安装。
安装完成后需要设置 `/etc/yum.repos.d/` 目录下的 `.repo` 相关源文件，在文件中插入优先级配置，`priority=N` ，`N` 为 1 到 99 正整数，值越小优先级越高。
![repository priority setting](http://pic.hqmmw.com/markdown-img-paste-20180613113014631.png)
