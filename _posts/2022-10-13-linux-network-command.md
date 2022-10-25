---
layout: post
title:  "Linux network 相關指令紀錄"
date:   2022-10-13 15:08:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: linux ip network
---

顯示機器routing
```sh
$ ip route
# or
$ route -n
```

列出docker網路
```sh
$ docker network ls
```

檢查docker網路詳細資訊
```
$ docker network inspect <network_id>
```

子網路區段最後一個IP(通常是255)是廣播
子網路區段倒數第二個IP(通常是254)是default gateway