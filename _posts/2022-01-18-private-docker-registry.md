---
layout: post
title:  "Private Docker Registry"
date:   2022-01-18 12:00:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: docker
---
建立一個自己的docker registry，這樣就不怕開發時，需要不斷將docker image推到docker hub造成超過儲存空間限制的問題。

# Run a local registry
```bash
$ docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

# Insecure registry
docker預設使用TLS連線到registry，所以必須透過設定來指定docker使用HTTP連線

在client端的/etc/docker/daemon.json新增設定如下:
```json
{
  "insecure-registries" : ["<registry_domain>:<port>"]
}
```

重新啟動docker

```bash
$ sudo systemctl restart docker
```

設定後docker連線該registry時會執行以下動作

- 嘗試使用HTTPS
  - 如果HTTPS可以連線到，但certificate有問題，則會直接忽略錯誤
  - 如果HTTPS不能連線到，嘗試使用HTTP

# Push to private docker registry
將需要推的docker image加上包含private registry domain的tag:
```bash
$ docker tag <original_image_name> <registry_domain>:<port>/<image_name>:<tag>
$ docker push <registry_domain>:<port>/<image_name>:<tag>
```

# Pull from private docker registry
```bash
$ docker pull <registry_domain>:<port>/<image_name>:<tag>
```

# In GitLab runner
直接使用Docker socket binding(mount `/var/run/docker.sock`)並在Host中的`/etc/docker/daemon.json`新增`insecure-registries`即可