---
title: Ubuntu安装docker-ce的详细教程
date: 2024-11-15 16:27:15
tags: Docker
category: Linux
---

<!-- @format -->

# Ubuntu 安装 docker-ce 的详细教程

## 一、卸载旧版本

```shell
sudo apt-get remove -y docker docker-engine docker.io containerd runc
```

## 二、更新软件包

```shell
sudo apt update
sudo apt upgrade
```

## 三、安装依赖包

```shell
sudo apt-get install ca-certificates curl gnupg lsb-release
```

## 四、添加 GPG 密钥

```shell
sudo curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

## 五、添加 Docker 仓库

```shell
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

## 六、更新软件包列表

```shell
sudo apt update
sudo apt upgrade
```

## 七、安装 Docker CE

```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

## 八、添加当前用户到 docker 组

```shell
sudo usermod -aG docker $USER
```

## 九、设置启动项和启动 Docker

```shell
sudo systemctl enable docker
sudo systemctl start docker
```

<!-- @format -->
