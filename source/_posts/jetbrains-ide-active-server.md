---
title: Jetbrains系列产品激活服务器搭建
date: 2017-11-10 17:12:32
tags:
- jetbrains
- webstorm
- phpstorm
categories:
- 工具
- IDE
---
@[TOC]

<!-- more -->

对于开发者来说，一款合用的开发工具非常影响其开发质量和速率，而纵观所有工具，开发IDE方面，Jetbrains 的产品值得我们称道。他设计的合语言开发 IDE，给我们提供了很好的开发体验，但这其中有一个很难为情的问题，对于我们开发者来说，其正版授权价格一时无法承受，特别是对于一些学习编程的初级入门者来说，就更为困难。
因此能有一种好的方式来使用该公司产品，也是对于个人体验，以及编程能力的提升。这里总结了一种自建服务器来激活全系列产品的方法。（当然，对于有能力的开发者来说，还是建议直接购买。）

开始，我们得先感谢这款工具的开发者，[**lanyus**][1]，目前该工具发布的版本已到 1.5 。
而针对于相关的使用方法，特别是当自己没有相关公网服务器的情况下，在自己本地机器上运行时，如何使用，有详细的说明，具体可以访问[**说明教程**][2]。
## 1 关键操作
> 1. [下载服务搭建程序][3]   备份链接: https://pan.baidu.com/s/1dGxchlJ 密码: 1k6w
> 2. 解压，使用 *IntelliJIDEALicenseServer_windows_amd64.exe*
> 3. 在相应 idea 注册界面选择 **License server**，填写 *http://127.0.0.1:1017*，（据说1017是作者女票生日，像作者女票致敬-_-）

![激活地址](http://pic.hqmmw.com/markdown-img-paste-2018122714350520.png)

## 2 自建公网服务器
1. 同样是下载[服务器搭建程序包][5]   备份链接: https://pan.baidu.com/s/1dGxchlJ 密码: 1k6w
2. 执行 *tar zxvf IntelliJIDEALicenseServer\(v1.5\).tar.gz*，解压文件，文件内容如下。

![服务器激活程序内容](http://pic.hqmmw.com/markdown-img-paste-20181227143625876.png)

3. 根据自身机器的系统情况，选择对应的程序，本例中是：linux 64 位系统，所以我将 *IntelliJIDEALicenseServer_linux_amd64* 单独提出，并重命名为 *IdeaServer*。（文件名太长，命令也太长）
4. 运行服务程序，*nohup ./IdeaServer -p 1024 -prolongationPeriod 9999999999999999 > idea.log 2>&1 &*，其中采用了 nohup 运行方式，并将日志记录在了 *idea.log* 文件中。
5. 在激活时，激活服务器的地址就是：*http://xxx.xxx.xxx.xx:1024*，其中有 x 就是你对应服务器的 IP 地址。

## 3 配置参数说明
1. ***-l***  指定绑定监听到具体哪个 ip (私用，不共享)
2. ***-u***  用户名参数，当未设置 -u 参数时，且计算机用户名为 *^\[a-zA-Z0-9]+$* 时，使用计算机用户名作为 idea 用户名
3. ***-p***  指定监听的端口
4. ***-prolongationPeriod***  指定过期时间参数

## 4 结合 nginx & 自有域名 配置
要记住某一服务器的 IP 地址，总是不那么容易，而相对的，记住一个相应的域名就要容易得多。
因此，我们如果有一个域名，如：*your.domain.com*，我们希望直接使用该域名地址来实现激活服务，但同时，对于 80 端口，一般常用的 WEB 服务，都是基于该端口来提供服务，因此，如果某一程序独占该端口过于浪费，这样，我们可以借用 nginx 的虚拟主机和反向代理能力，将 *your.domain.com:80* 上的请求，直接转发到我们的激活服务地址和端口。
```nginx
server {
  listen 80;
  server_name your.domain.com;

  location / {
    proxy_pass http://127.0.0.1:1024;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;

    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  access_log off;
  error_log /dev/null;
}
```
经过如上配置后，你就可以直接使用 *http://your.domain.com* 直接充当激活服务器了。



[1]: http://blog.lanyus.com/archives/314.html
[2]: http://blog.lanyus.com/archives/174.html
[3]: https://mega.nz/#!2w5WBL7I!OhsaQHOaW_IsUznu5loN3a-bSbLV--McOBqA-PM8EuY
[5]: https://mega.nz/#!2w5WBL7I!OhsaQHOaW_IsUznu5loN3a-bSbLV--McOBqA-PM8EuY
