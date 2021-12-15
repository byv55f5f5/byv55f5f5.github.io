---
layout: post
title:  "Docker指令紀錄"
date:   2021-12-15 16:30:00 +0800
categories: docker
author: "愛喝茶的熊"
tags: docker nginx
---

## 執行nginx docker
如果本機沒有nginx docker image就從docker hub尋找

```
$ sudo docker run --name docker_name -d -p 8080:80 nginx
```

Options:
  - -p: 將docker port給expose出來 ex: \${exposed_port}:${docker_port}
  - --name: 為即將執行的docker取名
  - -d: 背景執行此docker

## stop docker

```
$ sudo docker stop docker_name
```

## start docker

```
$ sudo docker start docker_name
```
