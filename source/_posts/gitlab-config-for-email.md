---
title: gitlab 配置腾讯企业邮箱邮件发送功能
date: 2018-03-23 10:18:42
tags:
- git
- gitlab
- 工具
- 邮件
categories:
- 工具
- gitlab
---

@[TOC]

<!-- more -->

在 gitlab 安装完成后，在实际的应用操作过程中，很多相关的功能依赖邮件服务，因此，将 gitlab 的邮件发送服务配置好，应用该工具很重要的一个部分。
在国内市场上，目前好用的邮箱产品有好几款，而这里所要使用的是 [腾讯企业邮箱][1] ，我们只需要有一个独立掌控的域名就非常容易开通一个。

## 1 背景

举例域名：`demo.com`
举例账号：`server@demo.com`
账号密码：`123456`
邮件发送显示名：`Server`

**注意**
在企业邮箱账号添加后，你需要登录一次邮箱，系统会让你再次修改一次密码，而这会影响之后的配置中的参数值。

登录到邮箱后，进入设置面板，需要将 SMTP 功能打开，以及相关的通讯端口也会有显示，如下图：
![QQ邮箱设置](http://pic.hqmmw.com/b217fba6bec7b316c3ec009ff2e1f3df.png)

## 2 gitlab 配置

配置文件路径：`/etc/gitlab/gitlab.rb`

```ruby {.line-numbers}
# gitlab 发信人
user['git_user_name'] = "Server"
user['git_user_email'] = "server@demo.com"
gitlab_rails['gitlab_email_from'] = 'server@demo.com'
gitlab_rails['gitlab_email_display_name'] = 'Server'

# 通过 SMTP 方式发送邮件
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
gitlab_rails['smtp_port'] = 465

# 邮箱账号&密码
gitlab_rails['smtp_user_name'] = "server@demo.com"
gitlab_rails['smtp_password'] = "123456"
gitlab_rails['smtp_domain'] = "exmail.qq.com"

gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = true
```

配置完成后，执行以下命令：

```bash
# 刷新 gitlab 配置
gitlab-ctl reconfigure
# 重启服务
gitlab-ctl restart
```

## 3 执行效果

当在 gitlab 平台操作 SSH Key 时，系统会发出如下邮件提醒：

![gitlab email](http://pic.hqmmw.com/bf1107f363e3099b84acad0ff3d8fedb.png)

[1]: https://exmail.qq.com
