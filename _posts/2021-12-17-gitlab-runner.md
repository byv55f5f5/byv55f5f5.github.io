---
layout: post
title:  "GitLab Runner (Docker Executor)"
date:   2021-12-17 16:00:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: docker gitlab-runner gitlab
---
一直以來都對GitLab runner感到好奇。好奇到底是誰在跑那些job / stage，所以就跟主管討論去熟悉一下這邊的建置

# Step To Install GitLab Runner
- 安裝ubuntu 20.04.3 (LTS) VM
  - minimal install
  - not install 3rd-party software
- 安裝GitLab Runner: [Install GitLab Runner using the \- official GitLab repositories \| GitLab](https://docs.gitlab.com/runner/install/linux-repository.html)
- 安裝Docker: [Install Docker Engine on Ubuntu \| Docker Documentation](https://docs.docker.com/engine/install/ubuntu/)
- 註冊GitLab Runner: [Registering runners \| GitLab](https://docs.gitlab.com/runner/register/index.html#linux)

# Build Docker By CI / CD
**TL;DR**

註冊GitLab runner時，使用以下command (Docker socket binding)
```bash
sudo gitlab-runner register -n \
  --url ${gitlab_url} \
  --registration-token ${registration_token} \
  --executor docker \
  --description "My Docker Runner" \
  --docker-image "docker:19.03.12" \
  --docker-volumes /var/run/docker.sock:/var/run/docker.sock
```

## Use the shell executor
直接使用GitLab Runner建立的環境去跑Job。該環境有哪些程式，在Job裡面就可以直接使用。

故只要在該環境安裝docker，Job就可以直接使用。
## Use the Docker executor with the Docker image (Docker-in-Docker)
> Services are designed to provide additional features which are network accessible. They may be a database like MySQL, or Redis, and even docker:stable-dind which allows you to use Docker-in-Docker.
It can be practically anything that’s required for the CI/CD job to proceed, and is accessed by network.
>
> When you use the dind service, you must instruct Docker to talk with the daemon started inside of the service.
The daemon is available with a network connection instead of the default /var/run/docker.sock socket.
Docker 19.03 does this automatically by setting the DOCKER_HOST in https://github.com/docker-library/docker/blob/d45051476babc297257df490d22cbd806f1b11e4/19.03/docker-entrypoint.sh#L23-L29

在GitLab Runner建立的環境上跑Container去跑CI/CD Job。使用Docker-in-Docker service，即可連線到"docker"這個host name如tcp://docker:2376/。

使用dind 要啟用 privileged mode才能正常運作。dind預設會使用 https://docker:2376/ 溝通操作，而不是預設的/var/run/docker.sock，所以需要引入docker-dind service。

> Specify to Docker where to create the certificates.
>
> Docker creates them automatically on boot, and creates`/certs/client` to share between the service and job container, thanks to volume mount from config.toml

在 Docker 19.03.12 版本後，預設會啟用TLS連線。需要mount "/certs/client" ，好讓dind service使用憑證。

- 編輯GitLab Runner設定檔 /etc/gitlab-runner/config.toml
  1. 將 runners.docker 底下的privileged 改成 true
  2. 新增 "/certs/client" 在 runners.dockers.volumes 底下
  3. example:

```toml
# /etc/gitlab-runner/config.toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "ubuntu-vm-on-qne-2"
  url = "https://sauron.qnap.com/"
  token = "p7JJWaLHC9CPYWZqwzmr"
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "docker:20.10.12"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/certs/client", "/cache"]
    shm_size = 0
```

- 在gitlab-ci.yml build docker的stage中加入docker-dind的services
- 加入變數DOCKER_TLS_CERTDIR: "/certs"

```yaml
#---

variables:
  DOCKER_TLS_CERTDIR: "/certs"

#---

pack-docker:
  image: docker:20.10.12
  services:
    - docker:20.10.12-dind
    # Use service (tcp://docker:2376 or tcp://docker:2375) to connect network
```

## Docker socket binding
把GitLab Runner上的docker的 /var/run/docker.sock mount在dind裡面。直接使用GitLab Runner上的docker運作。
- 編輯GitLab Runner設定檔 /etc/gitlab-runner/config.toml
    - 新增"/var/run/docker.sock:/var/run/docker.sock"在 runners.docker.volumes中
    - example:

```toml
# /etc/gitlab-runner/config.toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "ubuntu-vm-on-qne-2"
  url = "https://sauron.qnap.com/"
  token = "p7JJWaLHC9CPYWZqwzmr"
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "docker:20.10.12"
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
    shm_size = 0
```

## gitlab-ci.yml
```yaml
# 一次定義共用的設定，這樣就不需要在每個stage都重新定義一次
default:
  tags:
    - mfe-runner

variables:
  DOCKER_TLS_CERTDIR: "/certs"

# 定義 stage 執行順序
stages:
  - build
  - pack

build:
  # 定義該stage需要使用哪一個docker image
  image: node:16.13.1
  stage: build
  # 定義此stage最後需要保留的路徑，可以在GitLab下載該路徑的檔案，該路徑內的檔案會保留到下一個stage
  artifacts:
    paths:
      - dist
  # 定義執行的指令
  script:
    - ls
    - yarn install
    - yarn build

pack-docker:
  image: docker:20.10.12
  services:
    - docker:20.10.12-dind
  stage: pack
  artifacts:
    paths:
      - mfe-main.tar
  script:
    - ls
    - ls dist/
    - docker build -t mfe-main .
    - docker save mfe-main --output mfe-main.tar
```