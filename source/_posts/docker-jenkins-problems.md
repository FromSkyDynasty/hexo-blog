---
title: docker集成jenkins碰到的问题记录
date: 2018-05-05 14:40:46
tags:
    - docker
    - jenkins
    - dood
---

### 1. docker command is not found

在启动jenkins的时候挂载：

- `-v /bin/docker:/usr/bin/docker`
- `-v /var/run/docker.sock:/var/run/docker.sock`

### 2. libltd.so.7 No Such File ...

类似问题1: `-v /lib64/libltd.so.7:/usr/lib/libltd.so.7`

### 3. dial unix /var/run/docker.sock: connect: permission denied

#### 方法1: 将用户添加到`docker`用户组

`sudo usermod -a -G docker ${whoami}`

#### 方法2: 更改`/var/run/docker.sock`的权限

`sudo chmod 777 /var/run/docker.sock`




