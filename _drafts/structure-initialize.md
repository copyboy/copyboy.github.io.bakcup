---
layout: post
title: 架构师工具篇
categories: [软件, cate2]
description: 架构师工具篇
keywords: vagrant, virtualbox
---

## Vagrant

### 1）安装
```download
文件下载：


```
### 2）使用

配置vagrantfile
```
config.vm.box = "centos/7"
config.vm.network "public_network"
config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true
    # Customize the amount of memory on the VM:
    vb.memory = "2048"
    vb.name = "DeepJ-CentOS"
    vb.cpus = 2
end
```

添加本地镜像：
> vagrant box list
> vagrant box add centos/7 d:\vagrant-file\virtualbox.box
> vagrant box list

生成virtualbox镜像
> vagrant up

启动命令行
> vagrant ssh

开启root登录密码,设置密码，重启sshd服务
> vim /etc/ssh/sshd_config
> PasswordAuthentication yes
> passwd
> systemctl restart sshd

问题1： virtualbox 不能ping通主机地址，主机可以ping通虚拟机

开启阿里云加速器
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://gfidzzzc.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

> https://docs.docker.com/install/linux/docker-ce/centos/

安装docker
sudo yum install docker-ce docker-ce-cli containerd.io
启动docker
sudo systemctl start docker
docker加入开机启动
sudo systemctl enable docker



## VirtualBox 安装

```download
文件下载：

```

