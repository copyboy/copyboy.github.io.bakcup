---
layout: post
title: DevOps工具 - gitlab
categories: DevOps工具
description: GitLab搭建过程及基本使用
keywords: gitlab，git
---


# GitLab搭建过程及基本使用

``` 系统配置
系统： deepin 15.10.1
环境： 
    - ruby 2.3.3p222 (2016-11-21) [x86_64-linux-gnu]
    - java openjdk version "1.8.0_181"

```

```相关命令
查看内核/操作系统/CPU信息    uname -a
查看内核                   cat /proc/version
查看操作系统版本            cat /etc/issue
```


## 相关地址：

- [Github 开源地址](https://github.com/gitlabhq/gitlabhq)
- [GitLab 安装地址](https://about.gitlab.com/install/)

## 安装步骤

1. 安装依赖: openssh（SSH拉取） , ca, postfix （邮件通知）

```bash
sudo apt-get update
sudo apt-get install -y curl openssh-server ca-certificates
sudo apt-get install -y postfix
```
2. 使用debian方式获取安装包执行脚本，并安装 gitlab-ee , <b>此路不通<b>
```bash
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash

```
相关报错：
```
Installing /etc/apt/sources.list.d/gitlab_gitlab-ee.list...curl: (22) The requested URL returned error: 404 Not Found
```

3. 采用gitlab下载dkg安装包

[参考说明地址](https://www.jianshu.com/p/a9bb06a48b25)

[安装包下载地址](https://packages.gitlab.com/gitlab/gitlab-ce/)

我下载的版本是 gitlab-ce_12.0.2-ce.0_amd64.deb ，使用Jessie版本

查看当前系统使用的Debian版本， <b>*存疑*</b> <red>为什么是6.3 </red>? 后经确认，使用的是Debian8
```
cat /proc/version

> Linux version 4.15.0-30deepin-generic (pbuilder@zs-PC) (gcc version 6.3.0 20170516 (Debian 6.3.0-18+deb9u1)) #31 SMP Fri Nov 30 04:29:02 UTC 2018
```


```
Debian各版本 
- Debian 9（stretch） — 當前的穩定版
- Debian 8（jessie） — 被淘汰的穩定版
- Debian 7（wheezy） — 被淘汰的穩定版
- Debian 6.0（squeeze） — 被淘汰的穩定版
```

deb安装：
> sudo dpkg -i gitlab-ce_12.0.3-ce.0_amd64.deb

![安装截图](/images/posts/gitlab/安装截图.png)

4. 配置gitlab 使用的URL

修改配置
> sudo vi /etc/gitlab/gitlab.rb

![修改配置截图](/images/posts/gitlab/修改配置.png)

5. 加载gitlab配置，使其生效

> sudo gitlab-ctl reconfigure

![配置生效截图](/images/posts/gitlab/重新配置.png)

6. 查看配置生效结果

> sudo gitlab-ctl status

![配置状态截图](/images/posts/gitlab/查看配置状态.png)

7. 登录验证，修改密码

第一次登录时会提示让你修改root用户的密码

![修改密码](/images/posts/gitlab/改密码.png)

8. 登录

![root用户登录](/images/posts/gitlab/root-login.png)

9. 欢迎页

![欢迎页](/images/posts/gitlab/welcome-page.png)

10. 问题汇总

> 502 错误， 原因： 服务暂未启动好，等待一段时间后可以使用 gitlab-ctl status查看状态， 网上有说是占用太多资源，需要分配资源

![502](/images/posts/gitlab/gitlab-502.png)


> 系统卡顿： gitlab 占用内存较多， 我之前是2G的内存，后续分配到4G

11. 常用命令

gitlab 启动：
>  sudo gitlab-ctl start

gitlab 停止： 
> sudo gitlab-ctl stop

gitlab 重启：
>  sudo gitlab-ctl restart

