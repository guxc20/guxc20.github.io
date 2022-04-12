# Kubernetes
## 功能
* 基于容器的应用部署、维护和滚动升级
* 负载均衡和服务发现
* 跨机器和跨地区的集群调度
* 自动伸缩
* 无状态服务和有状态服务
* 广泛的 Volume 支持
* 插件机制保证扩展性

## 核心组件
* apiserver 提供了资源操作的唯一入口，并提供认证、授权、访问控制、API注册和发现等机制；
* controller manager 负责维护集群的状态，比如故障检测、自动扩展、滚动更新等；
* scheduler 负责资源的调度，按照预定的调度策略将Pod调度到相应的机器上；
* kubelet 负责维护容器的生命周期，同时也负责Volume（CVI）和网络（CNI）的管理；
* Container runtime 负责镜像管理以及Pod和容器的真正运行（CRI）；
* kube-proxy 负责为Service提供cluster内部的服务发现和负载均衡；
* etcd 保存了整个集群的状态；



有状态应用
案例：
Pod并非相同副本，有独立标识
Pod独立标识能对应固定网络标识，比如ip，发布升级后需要继续保持
每个pod有独立存储盘，发布升级后继续挂载原来的盘
应该发布时，按照固定顺序升级pod

这些需求deployment无法满足
所以通过statefulset，
Statefulset主要面向有状态应用管理，当然也可以对无状态应用管理：
每个pod有order序号，会按照序号创建删除更新pod
通过配置headless service，使每个pod有唯一网络标识
通过配置pvc template，每个pod有一块独享的pv存储盘
支持一定数量的灰度发布



Kubernetes




# kubernetes

### 功能：
调度（容器）、自动修复（检测宿主是否正常，转移）、水平伸缩（业务承载太多，进行扩容，负载均衡）

### 架构：
ui、cli--master--node（很多）

master（api server、controll、schedule、etcd）

node（pod、kubelet）

pod：包含一个或者多个容器

volume：声明pod中容器可访问的文件目录

deployment：一组pod的副本数目或者版本

service：提供访问一个或多个pod实例的稳定访问地址

namespaces：一个集群那边的逻辑隔离机制

### 容器本质：
视图被隔离、资源受限的进程

### 关系
kubernetes =操作系统

容器 = 进程

pod =进程组（一个helloworld由四个进程组成）

### pod
[pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) 在 k8s 的语境下一般不翻译。它的英文含义是“豆荚”，想像一个 container 是一个豆子，一个豆荚里有一到多个豆子，并组装成一个“豆角”。对应地，一个 pod 可以包含一个或多个 container（实际中我还没见多对应多个container 的情形）。

1. 它是 k8s 最小的调度单位
2. 每个 pod 都有自己的 IP，且 k8s 要保证 pod 间通过这个 IP 可以互相访问

跟 pod 相关的指令是平时用得最多的，例如：

* `kubectl get pods` 列出当前 namespace 下的所有 pod (namespace 后面讨论)
* `kubectl get pod my-pod -o yaml` 列出 `my-pod` 的配置
* `kubectl log my-pod` 列出 `my-pod` 的所有日志
* `kubectl log -f --since=10m my-pod` 列出 `my-pod` 近 10 分钟的日志并持续监控
* `kubectl describe pods my-pod` 查看 `my-pod` 的状态（如重启，上次失败原因等）
* `kubectl exec -it my-pod bash` “登录” my-pod 并执行 bash

### 总结

* container 可以理解成一个 docker 实例，里面跑着一个程序/服务
* pod 是 container 的抽象，有自己的 IP，不同 pod 网络互通，与 container 可以是一对一，也可以一对多
* ReplicaSet 是对多副本 Pod 的抽象，会自动启动、停止 Pod 来达到目标副本数，一般不直接使用
* Deployment 是一个控制概念，会创建、更新 ReplicaSet 从而实现 Pod 的部署、升级、回退、扩缩容等
* Service 屏蔽 Pod 细节，提供了统一的、稳定的接口，有自己的虚拟 IP(ClusterIP) 和端口，外部访问需要单独暴露接口（如 NodePort）
* ConfigMap 是对配置文件的管理，实现配置项和 Pod 的解耦，配置更新后需要重启 Pod
* namespace 是对 k8s 集群的资源做一个隔离

参考

b站阿里kubernetes入门教程

[kubernetes快速入门](https://lotabout.me/2020/Kubernetes-Introduction/)

