---
title: Centos rpm 包
date: 2018-06-12 10:11:23
tags:
    - centos
    - rpm
categories:
    - 系统环境
    - linux
---

@[TOC]

RPM 包，在 CentOS 使用过程中，也是经常会遇到的一种软件包形式，对于该类型包的相关操作进行了解，有助于日常的使用。

<!-- more -->

## 1 RPM 包与源码包的区别
**软件包分类**
- 源码包：C 源代码包
- rpm 包：编译之后的二进制包

**源码包**
- 优点：开源，并可以自由选择所需功能，可看源代码，卸载方便（直接删除安装位置）;
- 缺点：安装步骤繁琐，编译时间过长。

**RPM 包**
- 优点：使用简单，只需要几个命令就可以实现包的安装、升级、查询、卸载，安装速度快；
- 缺点：不能看源代码，功能选择不如源代码灵活，依赖性。

## 2 RPM 包命名 & 依赖性
**命名规则**
```bash
# 举例包名
httpd-2.4.6-67.el7.centos.x86_64.rpm
```
| 命名分段 | 说明 |
|:------|:-----|
| `httpd` | 软件包名 |
| `2.4.6` | 软件版本 |
| `67` | 发行次数 |
| `el7.centos` | 适合的 linux 平台 |
| `x86_64` | 适合的硬件平台 |
| `rpm` | 扩展名 |

**依赖性**
- 树形依赖：`a->b->c`，即 a 依赖 b ， b 依赖 c 。
- 环形依赖：`a->b->c->a`
- 模块依赖：模块依赖查询网站，[www.rpmfind.net][1]

## 3 RPM 包的安装、升级、卸载和查询
**包全名 ＆ 包名对比说明**
| 包全名 | 包名 |
|:-----|:-----|
| 例如：`httpd-2.4.6-67.el7.centos.x86_64.rpm` | 例如：`httpd` |
| 操作包时为没有安装的软件包时，使用 `包全名`。 | 操作包时为已经安装的软件包时，使用 `包名`。|
| 安装、升级时使用 | 查询、卸载时使用 |

**安装**
```bash
rpm -ivh [包全名]
# 选项：
#       -i(install)     安装
#       -v(verbose)     显示详细信息
#       -h(hash)        显示进度
#       --nodeps        不检测依赖性

# 如
rpm -ivh httpd-2.4.6-67.el7.centos.x86_64.rpm
```
> 注：可能会有很多依赖性问题出现，根据一个个依赖性继续 rpm 安装即可。

**升级**
```bash
rpm -Uvh [包全名]
# 选项：
#       -U(upgrade)     升级
```

**卸载**
```bash
rpm -e [包名]
# 选项：
#       -e(erase)       卸载
#       --nodeps        不检测依赖性

# 如
rpm -e httpd
# 报错信息
# 错误：依赖检测失败：
#       httpd = 2.4.6-67.el7.centos 被 (已安裝) httpd-devel-2.4.6-67.el7.centos.x86_64 需要
rpm -e httpd-devel
rpm -e httpd
```
> 注：卸载要按照安装依赖性的反方向卸载。

**查询**
```bash
# 查询是否安装
rpm -q [包名]
# 选项：
#       -q(query)       查询
#       -a(all)         所有

# 查询软件包的详细信息
rpm -qi [包名]
# 选项：
#       -i(information) 查询软件包信息

# 查询包中文件安装位置
rpm -ql [包名]
# 选项：
#       -l(list)        列表

# 查询系统文件属于哪个 rpm 包
rpm -qf [系统文件名]
# 选项：
#       -f(file)        查询系统文件属于哪个 rpm 包

# 查询软件包的依赖性
rpm -qR [包名]
# 选项：
#       -R(requires)    查询软件包的依赖性
```

[1]: http://www.rpmfind.net
