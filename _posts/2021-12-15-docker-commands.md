---
layout: post
title:  "Docker指令紀錄"
date:   2021-12-15 16:30:00 +0800
categories: docker
author: "愛喝茶的熊"
tags: docker nginx
---

# Docker 指令紀錄

## 基本操作

### 列出目前系統上的docker

```sh
$ sudo docker ps -a
```
Options:
  - -a: 列出全部，包含被停止的docker

### 執行nginx docker
如果本機沒有nginx docker image就從docker hub尋找

```
$ sudo docker run --name docker_name -d -p 8080:80 nginx
```

Options:
  - -p: 將docker port給expose出來 ex: \${exposed_port}:${docker_port}
  - --name: 為即將執行的docker取名
  - -d: 背景執行此docker

### stop docker

```
$ sudo docker stop docker_name
```

### start docker

```
$ sudo docker start docker_name
```

### 刪除停止運作的docker

```
$ sudo docker rm docker_name
```

### Copy 檔案
```sh
$ sudo docker cp ${src_path} ${container_name}:${dest_path}
# and
$ sudo docker cp ${container_name}:${src_path} ${dest_path}
```

## Dockerfile

### 建立Dockerfile，使用nginx為基底

```dockerfile
# Dockerfile
FROM nginx

COPY public/ /usr/share/nginx/html/
```

### 以Dockerfile build出docker image

```sh
$ sudo docker build -t docker_image_name .
```
Options:
  - -t: 為docker image上tag(命名)

### Run剛剛build出的docker image

```sh
$ sudo docker run --name docker_name -d -p 8080:80 docker_image_name
```
