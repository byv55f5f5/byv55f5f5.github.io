---
layout: post
title:  "Kubernetes 筆記"
date:   2022-01-12 11:00:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: docker k8s kubernetes
---
最近公司專案會需要使用到kubernetes(k8s)，就在這邊紀錄一下相關的名詞以及會使用到的指令。

# Pod
pod: K8s運作的最小單位，一個 Pod 對應到一個應用服務（Application）

## config
- .kind: Pod
- .metadata.name: 定義該pod的名稱
- .metadata.labels: 定義該pod相關標籤，提供給service去選取對應的pod
- .spec.containers: 定義該pod使用的docker相關資訊
- .spec.containers[\*].ports[\*].containerPort: 定義該pod expose出來的port

k8s config example:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mongo
  labels:
    app: mongo
spec:
  containers:
  - name: mongo
    image: mongo:4-focal
    ports:
    - containerPort: 27017
```

# Deployment
定義部屬pod的相關策略。

## Config
- .kind: 定義此config要啟用的k8s元件的類型
- .metadata.name: 定義deployment的名稱
- .spec.replicas: 定義要建多少個"複製"pods
- .spec.selector: 定義deployment要如何找到需要管理的pods
- .spec.template: 建出來的pods相關設定
    - .metadata.labels: 標註pod
    - .spec: 定義與pods相關配置

k8s config example:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: qrux-portal-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: qrux-portal-pods
  template:
    metadata:
      labels:
        app: qrux-portal-pods
    spec:
      containers:
        - name: qrux-portal-page
          image: qnapcharliehsu/qrux-portal-page:1.0.0
          ports:
            - name: http
              containerPort: 80
```

# Service
定義 Pods 如何被取用

## Config
- .spec.type: 定義service類型 \(\[ ClusterIP \| NodePort \| LoadBalancer \| ExternalName \]\)
- .spec.selector: 定義需要服務連結的pods的label
- .spec.ports[\*].port: 定義service需要開哪一個port
- .spec.ports[\*].targetPort: 定義需要連接pod的哪一個port

## Type:
- ClusterIP: expose service的cluster的內部IP，只能被cluster中的各元件存取
- NodePort: expose service在Node IP中固定的port上
- LoadBalancer: expose service在cloud所提供的load balancer上，在minikube上使用的話，需使用minikube tunnel讓minikube建立load balancer連線

## Notes:
- 使用kubectl port-forward 功能可以將minikube node上的service expose 出來

```bash
$ kubectl port-forward --address 0.0.0.0 service/qrux-portal-service 8080:8080
```

k8s config example:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: qrux-portal-service
spec:
  type: LoadBalancer
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 80
  selector:
    app: qrux-portal-pods
```


# StatefulSet
與deployment雷同，但是StatefulSet所創造出來的pod會有固定的名稱，不會因為重建而導致名稱往上疊加。搭配[Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)適合用來建立資料庫相關的服務。

# Commands
跑yaml檔(用來啟用deployments, services或pod等等)
```sh
$ kubectl apply -f ${k8s_yaml}
```

列出k8s pods
```
$ kubectl get pods
```

列出k8s deployments
```
$ kubectl get deployments
```

**可以使用kubectl get來列出各種k8s物件**

刪除使用該yaml檔跑的k8s物件
```sh
$ kubectl delete -f ${k8s_yaml}
```

觀察deployment/statefulset rollout的狀態
```sh
$ kubectl rollout status deployment/${deployment_name}
```

