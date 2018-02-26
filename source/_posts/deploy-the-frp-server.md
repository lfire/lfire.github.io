---
title: 部署FRP服务 实现内网穿透
date: 2017-11-15 14:06:38
tags:
- frp
- proxy
- 内网穿透
- 微信本地开发
categories:
- 环境配置
- 工具
---
@[TOC]

<!-- more -->

先抛出几个问题：
> 1. 我们有在本地开发的某一个 B/S 程序，希望给客户直接在线演示，怎么办？
> 2. 程序在内部开发调试阶段，对于某一个具体的测试问题，我不想发布到生产，只是想测试帮忙先看一下，OK 了后，我再发布，这样怎么办？
> 3. 微信公众号开发，现在这么火，烦人的，我需要调用微信公众号的接口，但微信的接口服务配置时，只接受一个公网的认证链接，这时候，难道我真的要每开发一个很小的变动，调试时仍需要不停的与线上部署服务器进行改动同步，就没有更简单的开发调试方法了吗？
> ......

现在来了，这些似乎难以搞定的事情，好像现在都有一定的共同性：

- 我的资源在内网环境，而且这样是为了方便我本人很好的修改，并对不同的需求进行快速的响应。
- 我需要将我本地的相关内容公开到公网环境去，方便相关的人直接查看到我本地的效果
- 与相关的第三方对接时，第三方只能识别公网上的相关资源，而不能穿透内网

**最最核心的问题是，在没有公网 IP 的情况下，我希望将我的资源挂载到公网。**

当然，这中间可能有很多的解决方案，比如：花生壳、ngrok、go-proxy等等，但这里我想分享的方案是 frp 服务，以这款工具所提供的能力来实现内网穿透，并选取我们以上所提到的一种场景——**微信开发调试**，来实际展示一个实例。

## 1 什么是 frp
[frp][1] 是一个高性能反向代理应用，可用于实现内网穿透，支持 TCP、UDP、HTTP、HTTPS 等协议，以此将内网资源对外网提供服务。
其中更详细的一些介绍，以及使用细节，官方的[《中文文档》][2] 有很详细的解释和说明，大家也可以直接参考。

## 2 准备
在使用之前，你需要准备一台拥有公网 IP 的服务器（如：阿里云、[linode][3]等），一台内网环境下的主机（一般就是你个人的 PC 机），一个域名（在服务器上开启虚拟主机，可以通过域名中的不同子域名，实现端口的重用），SSH 工具。
因国内的 VPS 一般需要通过备案后使用，使用会相对较为麻烦，因此，如果本身就有，就直接使用，如果需要新买，那最好是通过 [linode][4] 等这些相关的国外服务器平台购买，这样会比较方便。

## 3 服务端环境搭建
在网方的发布平台 [releases][5]，我们下载最新的发布版本（当前最新的版本为 V0.13.0）。其中我们需要选择对应的版本，其中服务器，常见的系统环境版本，一般为 linux 64 位，因此，我们一般情况选择：[frp_0.13.0_linux_amd64.tar.gz][6]。
我们将压缩包中的服务端程序（frps）和服务端配置文件（frps.ini）提取出来，如下图。

![image_1bv1q1duabqbh2md35d1irikp.png-4.6kB][7]

### 3.1 frp 配置
服务器的配置文件，官方手册中有详细的解释，我这里贴出本次我所搭环境的示例：
```ini
[common]
bind_port = 7000
vhost_http_port = 28088
subdomain_host = frp.domain.com
# 这里是为了安全的考虑，加入一个身份认证的 token 配置，这里只要服务端和客户端配置一致即可
privilege_token = xxxxxxxxxxxx

dashboard_port = 7500
dashboard_user = admin
dashboard_pwd = admin

log_file = ./frps.log

log_level = info
log_max_days = 3
```

### 3.2 nginx 反向代理配置
其中，因为我的服务器上有相关其他的程序共用，80 端口上交给了 nginx ，而在微信的接口配置时，只能是 80 or 443 端口，因此，这里我还借用了 nginx 的反向代理功能，配置如下：
```ini
server {
  listen 80;
  server_name frp.domain.com *.frp.domain.com;

  location / {
    proxy_pass http://frp.domain.com:28088;
    proxy_set_header Host $host;
    root /usr/share/nginx/html;
    index index.html index.htm index.php;
  }

  error_page 500 502 503 504 /50x.html;
  location = /50x.html {
    root /usr/share/nginx/html;
  }
}
```
这里启用了泛域名解析，考虑的是，这个服务开启后，我们可以通过这种方式提供给多人使用，不同的人员，可以启用一个对应的子域名，如：张三（zhangsan.frp.domain.com）、李四（lisi.frp.domain.com）、...

### 3.3 服务端启动
```shell
# 常规启动
./frps -c frps.ini
# 或者，使用 nohup 方式，启用后台运行模式，这样就可以避免命令行工具关闭后，服务中断的情况
nohup ./frps -c frps.ini &
```

## 4 客户端环境搭建
客户端在配置之前，同样的请在官方下载与自己系统对应的程序包，解压出其中的客户端程序（frpc.exe）和配置文件（frpc.ini）。

![image_1bv1si4m97b5j651olr1qf518pj16.png-5.2kB][8]

### 4.1 客户端环境配置
与服务端配置相对应的客户端配置如下：
```ini
[common]
# 公网服务器的 IP 地址
server_addr = xx.xx.xx.xx
server_port = 7000
# 这里需要与服务端所配置的 token 值一致
privilege_token = xxxxxxxxxxxx

[web]
type = http
local_port = 80
# 因启用了泛域名解析，不同的人员，可以使用各自不同的子域名，只要不重复，这样可以方便多人共用服务端资源
subdomain = liyz
custom_domains = frp.domain.com
```
示例中，我启用了我的个人子域名（liyz），那配置对应的访问域名为：liyz.frp.domain.com。

### 4.2 本机 wamp 环境 & 虚拟主机配置
示例中主要是针对 B/S 架构的应用进行解释，因此，这里我在本地通过 wamp 搭建了一个基本的 WEB 服务，并开启了一个相应的虚拟主机，配置如下：
```ini
<VirtualHost *:80>
    ServerName frp.domain.com
    ServerAlias liyz.frp.domain.com
    DocumentRoot c:\project\wumi\tmp\public
    <Directory c:\project\wumi\tmp\public\>
        Options Indexes FollowSymLinks
        AllowOverride all
        Order deny,allow
        Allow from all
    </Directory>
</VirtualHost>
```
这只是基本的服务配置，大家可以根据自己的实际应用需要，进行更多相关配置。

### 4.3 客户端启动
```shell
.\frpc.exe -c frpc.ini
```

![image_1bv1toms3iko1nvb1kvc6rg193d1j.png-15.4kB][9]


### 4.4 测试连接效果
公网服务器，启动 nginx，本地启动 wamp 服务，并在 wamp 虚拟主机配置的对应目录中（c:\project\wumi\tmp\public），新建 index.html 文件。浏览器打开： http://liyz.frp.domain.com 访问。

![image_1bv1tvjrc19asmle1vlf1a4h186c2g.png-7.5kB][10]

可以查看到正确的访问返回，成功！

## 5 微信开发调试环境搭建
[微信公众平台][11]中，关于接口配置信息的说明是，只支持：80 & 443，两个端口，这也就是前面，为什么要启用 nginx 服务的原因。
为了演示方便，这里仅采用[公众平台测试账号][12]。

### 5.1 服务程序开发
为了达到演示效果，这里在 thinkphp　的基础上，用 PHP 简单写了一个对接程序，基础代码如下：
```php
<?php
namespace app\index\controller;

use EasyWeChat\Foundation\Application;
use think\facade\Config;

class Index
{

  /**
   * 微信服务对接接口
   */
  public function wxServer()
  {
    $opt = Config::get('wechat.dev_server');
    $app = new Application($opt);
    $server = $app->server;
    $server->setMessageHandler(function ($message) {
      // $message->FromUserName // 用户的 openid
      // $message->MsgType // 消息类型：event, text....
      // 当 $message->MsgType 为 event 时为事件
      if ($message->MsgType == 'event') {
        switch ($message->Event) {
          case 'subscribe':
            return "感谢您关注！";
            break;
          default:
            break;
        }
      }
    });
    $response = $server->serve();
    $response->send();
  }

  /**
   * 测试调用微信 API
   */
  public function testWx()
  {
    $opt = Config::get('wechat.dev_server');
    $app = new Application($opt);
    $userService = $app->user;

    $users = $userService->lists();
    dump($users);

    $openId = 'oH9qBjuW0qt7Ub8kYN4G6PuZrLRw';
    $user = $userService->get($openId);
    dump($user);

//    $broadcast = $app->broadcast;
//    $r = $broadcast->sendText("大家好！");
//    dump($r);
  }

  /**
   * 服务首页
   */
  public function index()
  {
    echo "Hello, frp server ok!";
  }
}
```

简化微信的相关接口对接开发，这里使用了 [easywechat][13] 提供的封装程序，具体详细使用方法，可以参考其官网。

### 5.2 微信公众号配置
因开发了 PHP 对接程序，接口配置的 URL 为： http://liyz.frp.domain.com/Index/wxServer

![image_1bv1v6f091nmj1gbp1etpt5o1alk3a.png-24.1kB][14]

根据网速，大概最多几移后，系统会提示配置成功。

### 5.3 接口测试
在逻辑中，我们开发了一个 testWx 逻辑页面，我们可以通过访问，http://liyz.frp.domain.com/Index/testWx ，其中实现：获取公众号用户列表，以其中一个用户的信息。

![image_1bv1vdcif1033msejimhh44854n.png-54.2kB][15]

需要说明的，公众号测试平台中的某些接口，在实际开发过程中，是没有相关权限的，比如：群发接口，因此，建议在有资源的情况下，还是单独的申请注册一个认证的订阅号，或是服务号来开展测试调试工作。

## 6 几个有用的点
1. 80 端口共用，在一般的使用场景中，绝大多数的 WEB 服务是基于 80 端口提供服务的，因此，我们的 frp 不大可能在某一服务器上独占 80 端口资源，因此，这里启用了 28088 端口的兼听，通过 nginx 反向代理将某一域名的 80 端口请求转发到 28088。
2. 泛域名解析，再结合 frp 的 *subdomain* 配置，实现多人共用 frp 服务器资源。
3. 通过 frp 的转发，能实现本地基于域名的虚拟主机服务，示例中是通过 wamp 的服务实现的，当然，你也可以安装 nginx 等其他相关服务程序。


  [1]: https://github.com/fatedier/frp
  [2]: https://github.com/fatedier/frp/blob/master/README_zh.md
  [3]: https://www.linode.com/?r=6c99252e8f84978356850c65fd5f5fbede94b08a
  [4]: https://www.linode.com/?r=6c99252e8f84978356850c65fd5f5fbede94b08a
  [5]: https://github.com/fatedier/frp/releases
  [6]: https://github.com/fatedier/frp/releases/download/v0.13.0/frp_0.13.0_linux_amd64.tar.gz
  [7]: http://static.zybuluo.com/lfire/murabcptj4blu5ywxp6decls/image_1bv1q1duabqbh2md35d1irikp.png
  [8]: http://static.zybuluo.com/lfire/wnykl2702ir8vdita9dq5sud/image_1bv1si4m97b5j651olr1qf518pj16.png
  [9]: http://static.zybuluo.com/lfire/lm0sfdwh0w7pi6xzh2c2xrzj/image_1bv1toms3iko1nvb1kvc6rg193d1j.png
  [10]: http://static.zybuluo.com/lfire/bwitmi0pr0m2oceicp12gzb1/image_1bv1tvjrc19asmle1vlf1a4h186c2g.png
  [11]: https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1445241432
  [12]: http://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login
  [13]: https://easywechat.org/
  [14]: http://static.zybuluo.com/lfire/hmgwuoytk1k3dnuoj4lv962a/image_1bv1v6f091nmj1gbp1etpt5o1alk3a.png
  [15]: http://static.zybuluo.com/lfire/4cmcly15lzyu5hp0vhb53fw6/image_1bv1vdcif1033msejimhh44854n.png
