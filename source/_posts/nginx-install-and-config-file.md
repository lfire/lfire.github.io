---
title: nginx 安装和配置文件
date: 2018-06-21 16:45:10
tags:
    - nginx
categories:
    - 环境配置
    - nginx
---

@[TOC]

在常规的使用过程中， nginx 是经常会要用到的一种服务软件，其给基于 web 的服务系统，提供了很大的便利，以及相关方面的基础支持。本文记录一下，在 linux 环境下，安装和配置 nginx 服务的过程。

<!-- more -->

## 1 nginx 安装
基础工作的基础，就是先要会安装软件本身—— nginx ，一般分为两种，一种是下载源码包，自行编译安装；另一种，则是通过 yum 源安装。
在 {% post_link centos-yum-repository CentOS yum 源 %} 一文中，有提到关于 nginx 源如何添加的方法，这里不再赘叙，不清楚的，可以查看相应的文章。
添加源完成后，剩下的安装操作就很简单了：
```bash
# 默认安装的是源中最新的版本
# 同样也可以指定版本安装，前提需要你先去 packages 中查看存在某一具体版本
yum install nginx -y
```

## 2 nginx 配置
安装成功后，我们仍需要对其进行相关配置，以满足我们的相关应用需求。

### 2.1 配置文件 `nginx.conf`
nginx 配置文件主要分为四部分：

- `main` ：全局配置
- `server` ：主机配置
- `upstream` ：上游服务器配置，主要是：反向代理、负载均衡相关配置
- `location` ：URL 路由匹配规则配置

其中 `main` 部分的配置指令，会在全局范围内产生作用，从而影响其他所有部分的配置；`server` 部分的配置，主要用于指定虚拟主机域名、IP 和端口；`upstream` 用于配置一系列的后端服务器，配置反向代理及后端服务器的负载均衡；`location` 部分用于配置匹配页面访问规则（如，根目录：`/`，`/images`，等）。
这几部分之间的关系是：`server` 继承 `main`，`location` 继承 `server`；`upstream` 既不会继承其他配置也不会被继承，其有一套自身特有的配置。

### 2.2 常用配置说明
在配置 nginx 时，一般简单的应用，只需要配置很少量的，常见的几个配置参数即可，以下对于常见的一些配置进行说明记录。

#### 2.2.1 `main` 全局配置
nginx 在运行时，与具体业务功能（如 http 服务或是 email 服务代理等）无关的一些参数，如：工作进程数、运行的身份等。

- `worker_processes 2`
  在配置文件的顶部 `main` 部分，worker 角色的工作进程的个数，master 进程是接收并分配请求给 worker 处理。这个数值简单点可以配置为 CPU 的核心数（`grep ^processor /proc/cpuinfo | wc -l`），也是 auto 值，如果开启了 ssl 和 gzip ，则更应该设置成与逻辑 CPU 数量一致甚至为 2 倍，可以减少 I/O 操作，如若 nginx 服务器还有其它服务运行，可以适当的考虑减少。
- `worker_cpu_affinity`
  同样配置在 `main` 部分。在高并发场景下，通过配置 CPU 粘性来降低由于多 CPU 核心切换造成的寄存器等现场重建带来的性能损耗。如：`worker_cpu_affinity 0001 0010 0100 1000;` （四核）。
- `worker_connections 2048`
  配置在 `events` 部分。每一个 worker 进程能并发处理（发起）的最大连接数（包含与客户端或后端被代理服务器间等所有连接数）。nginx 作为反向代理服务器，其计算公式是：
  ***`最大连接数 = worker_processes * worker_connections / 4`***
  所以这里客户端最大连接数是 1024 ，这个可以增加到 8192 都没关系，看情况而定，但不能超过后面的 `worker_rlimit_nofile`。当 nginx 作为 http 服务器时，计算公式里面是除以 2。
- `worker_rlimit_nofile 10240`
  配置在 `main` 部分。配置是没有配置，可以限制为操作系统最大的限制 65535。
- 'use epoll'
  配置在 `events` 部分。在 Linux 操作系统中，nginx 默认使用 epoll 事件模型，得益于此，nginx 在 Linux 操作系统中效率相当高。同时 nginx 在 OpenBSD 或 FreeBSD 操作系统上采用类似于 epoll 的高效事件模型 kqueue。在操作系统不支持这些高效模型时才使用 select。

#### 2.2.2 http 服务器
利用 nginx 提供 http 服务非常常用，该部分的配置是与提供 http 服务相关的一些配置参数。如：是否使用 keepalive ，是否开户 gzip 进行压缩等。

- `sendfile on`
  开启高效文件传输模式，`sendfile` 配置是指定 nginx 是否调用 sendfile 函数来输出文件，以减少用户空间到内核空间的上下文切换。对于普通应用设为 `on`，如果用来进行下载等应用，磁盘 IO 重负载应用，可以配置为 `off`，以平衡磁盘与网络 I/O 处理速度，降低系统负载。
- `keepalive_timeout 65`
  长连接超时时间，单位是秒。这个参数很敏感，涉及浏览器的种类、后端服务器的超时配置、操作系统的配置，甚至可以单独立一个章节。长连接请求大量小文件的时候，可以减少重建连接的开销，但如果有大文件上传，65s 内没有上传完成会导致失败。如果配置时间过长，用户又多，长时间保持连接会占用大量资源。
- `send_timeout`
  用于指定响应客户端的超时时间。这个超时仅限于两个连接活动之间的时间，如果超过个这时间，客户端没有任何活动，nginx 将会关闭连接。
- 'client_max_body_size 10m'
  允许客户端请求的最大单文件字节数。如果有上传较大文件，需要配置它的限制值。
- `client_body_buffer_size 128k`
  缓冲区代理缓冲用户端请求的最大字节数。

**模块 http_proxy**
http_proxy 是用于实现 nginx 反向代理服务器的功能，其中还涉及到缓存等。

- `proxy_connect_timeout 60`
  nginx 跟后端服务器连接的超时时间（代理连接超时）
- `proxy_read_timeout 60`
  连接成功后，与后端服务器两个成功的响应操作之间超时时间（代理接收超时）
- `proxy_buffer_size 4k`
  配置代理服务器（nginx）从后端 realserver 读取并保存用户头信息的缓冲区大小，默认与 `proxy_buffers` 大小相同，其实可以将这个配置值配得略小一点。
- `proxy_buffers 4 32k`
  `proxy_buffers` 缓冲区大小和数量，nginx 针对单个连接缓存来自后端 realserver 的响应，网页平均在 32k 以下的场景，就这样配置。
- `proxy_busy_buffers_size 64k`
  高负载下缓冲大小（`proxy_buffers * 2`）
- `proxy_max_temp_file_size`
  当 `proxy_buffers` 放不下后端服务器的响应内容时，会将一部分保存到硬盘的临时文件中，这个值用来配置最大临时文件大小，默认 1024M ，它与 `proxy_cache` 没有关系。大于这个值，将从 upstream 服务器传回。配置为 `0` 代表禁用。
- `proxy_temp_file_write_size 64k`
  当缓存被代理的服务器响应到临时文件时，这个选项限制每次写临时文件的大小。`proxy_temp_path` （可以在编译的时候）指定写到哪个目录。

`proxy_pass`，`proxy_redirect` 见 [location 规则部分](#224-location-规则)

**模块 http_gzip**
http_gzip 主要用于配置 gzip 压缩相关参数，以此提升网络传输效率。

- `gzip on`
  开启 gzip 压缩输出，减少网络传输。
- `gzip_min_length 1k`
  配置允许压缩的页面最小字节数，页面字节数从 header 头信息中的 content-length 进行获取。默认值是 20，建议配置大于 1k 的字节数，小于 1k 可能会越压越大。
- `gzip_buffers 4 16k`
  配置系统获取几个单位的缓存，用于存储 gzip 的压缩结果数据流。`4 16k` 代表以 16k 为单位，安装原始数据大小以 16k 为单位的 4 倍申请内存。
- `gzip_http_version 1.0`
  用于识别 http 协议的版本，早期的浏览器不支持 gzip 压缩，用户就会看到乱码，所以为了支持前期版本加上了这个选项，如果启用了 nginx 的反向代理，并期望也启用 gzip 压缩的话，由于末端通信是 http/1.0，故请配置为 `1.0` 。
- `gzip_comp_level 6`
  gzip 压缩比，`1` 压缩比最小，处理速度最快，`9` 压缩比最大，但处理速度最慢（传输快但比较耗 CPU）。
- `gzip_types`
  匹配 mime 类型进行压缩，无论是否指定，`text/html` 类型总是会被压缩的。
- `gzip_proxied any`
  nginx 作为反向代理的时候启用，决定开户或是关闭后端服务器返回的结果是否压缩，匹配的前提是后端服务器必须要返回包含 `via` 的 header 头。
- `gzip_vary on`
  与 http 头有关系，会在响应头加个 `Vary:Accept-Encoding` ，可以让前端的缓存服务器缓存经过 gzip 压缩的页面，如：用 Squid 缓存经过 nginx 压缩的数据。

#### 2.2.3 server 虚拟主机
http 服务上支持若干虚拟主机，每个虚拟主机对应一份 server 配置，配置中包含该虚拟主机相关的配置。在提供 mail 服务代理时，也可以建立若干 server ，每个 server 通过监听地址和端口来区分。

- `listen`
  监听端口，默认 `80` ，小于 1024 的要以 root 启动。可以是 `listen *:80` ， `listen localhost:80` 等形式。
- `server_name`
  服务器名，如 `loclhost` ， `www.domain.com` ，支持正则匹配。

**模块 http_stream**
http_stream 模块通过一个简单的调试算法来实现客户端 IP 到后端服务器的负载均衡，`upstream` 后接负载均衡器的名字，后端 realserver 以 `host:port options;` 方式组织在 `{}` 中。如若后端被代理的只有一台，也可以直接写在 `proxy_pass` 。

#### 2.2.4 location 规则
http 服务中，某些特定的 URL 对应的一系列配置。

- `root /var/www/html`

- `index index.html index.htm index.php`
- `proxy_pass http:/backend`
- `proxy_redirect off;`
  `proxy_set_header Host $host;`
  `proxy_set_header X-Real-IP $remote_addr;`
  `proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;`


### 2.3 其他配置

#### 2.3.1 访问控制 `allow/deny`


#### 2.3.2 目录列表 `autoindex`
