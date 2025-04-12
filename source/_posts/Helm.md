---
title: Helm
date: 2024-07-09 15:35:49
tags: [云原生, helm]
categories: 云原生
---

## 安装使用
```plain
 wget https://get.helm.sh/helm-v3.13.2-linux-amd64.tar.gz
 tar -zxvf helm-v3.13.2-linux-amd64.tar.gz
 mv linux-amd64/helm /usr/local/bin/helm
 helm version
 
 helm repo add bitnami https://charts.bitnami.com/bitnami
 helm repo update
 helm search repo wordpress //测试
 helm install mywordpress stable/wordpress //安装
 helm list


 或者
 helm pull volcano-sh/volcano	//包下载到本地
 helm install volcano ./volcano-1.8.1.tgz -n volcano-system //使用本地包部署
```

## repo
### helm repo -h
```plain
guxc@192 ~ % helm repo -h
Available Commands:
  add         add a chart repository
  index       generate an index file given a directory containing packaged charts
  list        list chart repositories
  remove      remove one or more chart repositories
  update      update information of available charts locally from chart repositories
```

### helm repo list
查看本地helm仓库

```plain
guxc@192 ~ % helm repo list
NAME   	URL
bitnami	https://charts.bitnami.com/bitnami
```

### helm repo add
添加仓库

```plain
guxc@192 ~ % helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
```

### helm repo update
更新仓库内容

```plain
guxc@192 ~ % helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "bitnami" chart repository
Update Complete. ⎈Happy Helming!⎈
```

## chart
### helm search repo
查询有哪些chart可用

```plain
guxc@192 ~ % helm search repo mysql
NAME                  	CHART VERSION	APP VERSION	DESCRIPTION
bitnami/mysql         	9.12.1       	8.0.34     	MySQL is a fast, reliable, scalable, and easy t...
bitnami/phpmyadmin    	12.1.1       	5.2.1      	phpMyAdmin is a free software tool written in P...
bitnami/mariadb       	13.1.3       	11.0.3     	MariaDB is an open source, community-developed ...
bitnami/mariadb-galera	9.1.2        	11.0.3     	MariaDB Galera is a multi-primary database clus...
```

### helm show 
查看chart详细信息，也叫inspect，后面可以跟all，chart，values等

查看chart细节如下，查询values，则是helm show values bitnami/mysql,查询所有，则是helm show all

```plain
guxc@192 ~ % helm show chart bitnami/mysql
annotations:
  category: Database
  images: |
    - name: mysql
      image: docker.io/bitnami/mysql:8.0.34-debian-11-r31
    - name: mysqld-exporter
      image: docker.io/bitnami/mysqld-exporter:0.15.0-debian-11-r24
    - name: os-shell
      image: docker.io/bitnami/os-shell:11-debian-11-r43
  licenses: Apache-2.0
apiVersion: v2
appVersion: 8.0.34
dependencies:
- name: common
  repository: oci://registry-1.docker.io/bitnamicharts
  tags:
  - bitnami-common
  version: 2.x.x
description: MySQL is a fast, reliable, scalable, and easy to use open source relational
  database system. Designed to handle mission-critical, heavy-load production applications.
home: https://bitnami.com
icon: https://bitnami.com/assets/stacks/mysql/img/mysql-stack-220x234.png
keywords:
- mysql
- database
- sql
- cluster
- high availability
maintainers:
- name: VMware, Inc.
  url: https://github.com/bitnami/charts
name: mysql
sources:
- https://github.com/bitnami/charts/tree/main/bitnami/mysql
version: 9.12.1
```

### helm create
创建chart包



### helm pull
<font style="color:rgb(79, 79, 79);">从仓库拉取chart包</font>

<font style="color:rgb(79, 79, 79);"></font>

### helm lint
```azure
# 检查图表以查找可能的问题
helm lint path
# 如果在chart包外
helm lint chartname  
# 如果在chart包内
helm lint .

```
### helm test
### helm package
打成一个压缩包

### helm install
安装chart包，将生成一个release

-f 指定 yaml 文件

```plain
helm install
helm install -f 
helm install --dry-run --debug

```

## release
### helm list
查看release列表，也称ls
```azure
获取指定命名空间的发布列表：  
helm list -n namespace-name

获取所有命令空间的发布列表  
helm list -A
```


```plain
$ helm ls
NAME                             	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART                                 	APP VERSION
argo-artifacts                   	default  	1       	2023-05-12 15:32:32.186395424 +0800 CST	deployed	minio-8.0.10                          	master
mongodb-13.9.4                   	default  	1       	2023-08-04 10:20:24.614255772 +0800 CST	failed  	mongodb-13.9.4                        	6.0.5

$ helm list
NAME                             	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART                                 	APP VERSION
argo-artifacts                   	default  	1       	2023-05-12 15:32:32.186395424 +0800 CST	deployed	minio-8.0.10                          	master
mongodb-13.9.4                   	default  	1       	2023-08-04 10:20:24.614255772 +0800 CST	failed  	mongodb-13.9.4                        	6.0.5

$ helm list -A
NAME                             	NAMESPACE              	REVISION	UPDATED                                	STATUS  	CHART                                 	APP VERSION
argo-artifacts                   	default                	1       	2023-05-12 15:32:32.186395424 +0800 CST	deployed	minio-8.0.10                          	master
mongodb-13.9.4                   	default                	1       	2023-08-04 10:20:24.614255772 +0800 CST	failed  	mongodb-13.9.4                        	6.0.5
mysql                            	chief-ai-centerplatform	1       	2022-12-27 17:03:19.871762933 +0800 CST	deployed	mysql-9.4.5                           	8.0.31
mysql                            	chief-lab              	1       	2022-12-22 12:15:55.476730674 +0800 CST	deployed	mysql-9.4.5                           	8.0.31

```

### helm upgrade


### helm uninstall 
删除release 实例

有没有delete

### helm status
获取release 状态



### helm rollback
回滚到某一个版本

```plain
helm rollback mydb 1
```

### helm history
查看release 历史版本



## 其他
### help
```plain
helm help
```

### env
```plain
helm env
```

