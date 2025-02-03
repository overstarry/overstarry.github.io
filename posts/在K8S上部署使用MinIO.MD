+++
title = "在K8S上部署使用MinIO Operator"
date = 2021-10-28T15:30:10+08:00
draft = true
[taxonomies]
tags = ["kubernetes", "MinIO"]
+++

本文介绍如何在K8S集群上部署 MinIO  Operator

## 步骤

### 0 安装 MinIO Kubernetes Operator
需提前安装 Kubernetes krew插件管理器，这里就不多叙述。
运行以下命令，使用Kubernetes krew插件管理器安装MinIO Operator和Plugin
```console
kubectl krew update
kubectl krew install minio
```

你会看到一下信息表示安装成功。
```log
Updated the local copy of plugin index.
Installing plugin: minio
Installed plugin: minio
\
 | Use this plugin:
 | 	kubectl minio
 | Documentation:
 | 	https://github.com/minio/operator/tree/master/kubectl-minio
 | Caveats:
 | \
 |  | * For resources that are not in default namespace, currently you must
 |  |   specify -n/--namespace explicitly (the current namespace setting is not
 |  |   yet used).
 | /
/
WARNING: You installed plugin "minio" from the krew-index plugin repository.
   These plugins are not audited for security by the Krew maintainers.
   Run them at your own risk.
```
```console
kubectl minio version
v4.3.1
```

### 1 初始化 the MinIO Kubernetes Operator

运行命令初始化 MinIO Operator：kubectl minio init
```console
kubectl minio init
```

该命令使用以下默认设置初始化 MinIO Operator：

将 Operator 部署到minio-operator命名空间中。指定参数以将运算符部署到不同的命名空间。kubectl minio init --namespace

使用cluster.local配置运营商的DNS主机名，当作为集群域。指定 参数以设置不同的集群域值。kubectl minio init --cluster-domain

```log
namespace/minio-operator created
serviceaccount/minio-operator created
clusterrole.rbac.authorization.k8s.io/minio-operator-role created
clusterrolebinding.rbac.authorization.k8s.io/minio-operator-binding created
customresourcedefinition.apiextensions.k8s.io/tenants.minio.min.io created
service/operator created
deployment.apps/minio-operator created
serviceaccount/console-sa created
clusterrole.rbac.authorization.k8s.io/console-sa-role created
clusterrolebinding.rbac.authorization.k8s.io/console-sa-binding created
configmap/console-env created
service/console created
deployment.apps/console created
+++--------------

To open Operator UI, start a port forward using this command:

kubectl minio proxy -n minio-operator 

+++--------------

```

### 验证安装 

使用命令 ```kubectl get all --namespace minio-operator``` 验证是否安装成功，如果出现以下场景，就表示安装成功。
```log
NAME                                 READY   STATUS    RESTARTS   AGE
pod/console-789ff4b97b-72prg         1/1     Running   0          7m26s
pod/minio-operator-f6fd69b6d-g75ml   1/1     Running   0          7m26s
pod/minio-operator-f6fd69b6d-v7rbh   1/1     Running   0          7m26s

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
service/console    ClusterIP   10.97.211.101   <none>        9090/TCP,9443/TCP   7m26s
service/operator   ClusterIP   10.105.166.6    <none>        4222/TCP            7m26s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/console          1/1     1            1           7m26s
deployment.apps/minio-operator   2/2     2            2           7m26s

NAME                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/console-789ff4b97b         1         1         1       7m26s
replicaset.apps/minio-operator-f6fd69b6d   2         2         2       7m26s

```