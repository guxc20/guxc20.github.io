# # docker
构成：镜像，容器，仓库

## 镜像
### pull
Docker  运行容器前需要本地存在对应的镜像，
 如果镜像不存在，Docker 会尝试先从默认镜像仓库下载（默认使用 Docker Hub  公共注册服务器中的仓库），
 用户也可以通过配置，使用自定义的镜像仓库。
不指定默认latest 镜像

```
//不指定相当于docker pull registry.hub.docker.com/ubuntu:18.04
//即从默认的注册服务器DockerHub Registry中的ubuntu仓库来下载镜像

[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker pull ubuntu:18.04
18.04: Pulling from library/ubuntu
284055322776: Pull complete 
Digest: sha256:0fedbd5bd9fb72089c7bbca476949e10593cebed9b1fb9edf5b79dbbacddd7d6
Status: Downloaded newer image for ubuntu:18.04
docker.io/library/ubuntu:18.04

//如果从非官方的仓库下载，则需要在仓库名称前指定完整的仓库地址
//从网易蜂巢镜像源来下载
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker pull hub.c.163.com/public/ubuntu:18.04
```

### run

下载镜像到本地后， 即可随时使用该镜像了， 例如利用该镜像创建一个容器,查看ubuntu的文件

```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker run -it ubuntu:18.04 bash
root@1549d94cc56c:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

//退出
root@1549d94cc56c:/# exit
exit
[root@iZm5e8waxa372qgjp6rzo3Z ~]#
```

### images
列出本地主机已有镜像
```
guxuchengdeMacBook-Pro:notes guxc$ docker images
REPOSITORY                                                                    TAG             IMAGE ID       CREATED        SIZE
cn-app-hosting                                                                1.0.0-pre       c3deea817318   3 weeks ago    118MB

```

### tag
添加镜像标签

```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker tag ubuntu:18.04 test:latest
```

### inspect
获取该镜像的详细信息
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker inspect test
[
    {
        "Id": "sha256:5a214d77f5d747e6ed81632310baa6190301feeb875cf6bf9da560108fa09972",
        "RepoTags": [
            "test:latest",
            "ubuntu:18.04"
        ],
        "RepoDigests": [
            "ubuntu@sha256:0fedbd5bd9fb72089c7bbca476949e10593cebed9b1fb9edf5b79dbbacddd7d6"
        ],
        "Parent": "",
        "Comment": "",
```

### history
查看镜像历史，各个层的创建信息
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker history test
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
5a214d77f5d7        4 months ago        /bin/sh -c #(nop)  CMD ["bash"]                 0B                  
<missing>           4 months ago        /bin/sh -c #(nop) ADD file:0d82cd095966e8ee7…   63.1MB  
```


### search
搜索docker hub官方库中的镜像
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker search nginx
NAME                                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
bitnami/nginx                                     Bitnami nginx Docker Image                      117                                     [OK]
bitnami/wordpress-nginx                           Bitnami Docker Image for WordPress with NGINX   54                                      [OK]
```

### rmi
删除镜像
```
//有多个标签，删标签，一个标签，删镜像
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker rmi test:latest
Untagged: test:latest

//有容器存在，无法删，需先删容器，再删镜像
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker rmi ubuntu:18.04
Error response from daemon: conflict: unable to remove repository reference "ubuntu:18.04" (must force) - container 1549d94cc56c is using its referenced image 5a214d77f5d7

//docker ps -a 查看容器
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker ps -a

//先删容器
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker rm 1549d94cc56c
1549d94cc56c

//再删镜像
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker rmi 5a214d77f5d7
Untagged: ubuntu:18.04
Untagged: ubuntu@sha256:0fedbd5bd9fb72089c7bbca476949e10593cebed9b1fb9edf5b79dbbacddd7d6
Deleted: sha256:5a214d77f5d747e6ed81632310baa6190301feeb875cf6bf9da560108fa09972
Deleted: sha256:824bf068fd3dc3ad967022f187d85250eb052f61fe158486b2df4e002f6f984e
```

### prune
清理镜像
清理遗留的临时镜像文件以及没有被使用的镜像
-a, -all: 删除所有无用镜像， 不光是临时镜像
-f, -force: 强制删除镜像， 而不进行提示确认
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker image prune -f
Total reclaimed space: 0B
```


### save
将镜像导出到本地
-o 导出到指定文件
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker save -o my_ubuntu.tar ubuntu:latest
[root@iZm5e8waxa372qgjp6rzo3Z ~]# ls
my_ubuntu.tar
```

### load
载入镜像
将tar镜像文件导入到本地镜像库
-i 从指定文件读入镜像内容
```
//如果已存在，不会导入
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker load -i my_ubuntu.tar
9f54eef41275: Loading layer [==================================================>]  75.16MB/75.16MB
Loaded image: ubuntu:latest 
```


### commit
基于已有的容器创建镜像
-a 作者信息
-m提交消息
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker run -it ubuntu:latest bash
root@fbe508dc57dd:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@fbe508dc57dd:/# touch test

//基于ubuntu创建镜像newtest:1.1
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker commit -m "add test" -a "newtest" fbe508dc57dd newtest:1.1
sha256:63075c4768acbce5d5b96d547b843c2160719c4e8530328ab76171ae07910f2d
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker images
REPOSITORY                                                                                                TAG                                                               IMAGE ID            CREATED             SIZE
newtest                                                                                                   1.1                                                               63075c4768ac        6 seconds ago       72.8MB

//查看newtest
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker run -it 63075c4768ac bash
root@795d0f2319ba:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  test  tmp  usr  var
```

### push
上传镜像到仓库，默认dockerhub
```
//先打标签
docker tag test:latest usertest:latest
//push
docker push usertest:latest
```


## 容器
### create
新建容器,刚新建的容器处于停止状态
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker create ubuntu:latest
48bf73075a35dc43df899b5eee5f17c5895e717430c9887438d19f035a0e27a9
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker ps -a
CONTAINER ID        IMAGE                                                                                                     COMMAND                   CREATED             STATUS                     PORTS                    NAMES
48bf73075a35        ubuntu:latest                                                                                             "bash"                    6 seconds ago       Created                                             magical_benz
```

### start
启动容器，启动一个刚创建的容器
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker start 48bf73075a35
48bf73075a35
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker ps -a
CONTAINER ID        IMAGE                                                                                                     COMMAND                   CREATED              STATUS                     PORTS                    NAMES
48bf73075a35        ubuntu:latest                                                                                             "bash"                    About a minute ago   Exited (0) 5 seconds ago                            magical_benz
```

### run
新建并启动容器，等价于create+start
> -i 让容器标准输入保持打开  
> -t 让docker分配一个伪终端并绑定到容器的标准输入上  
> —rm，容器退出时就能够自动清理容器内部的文件系统  
> Bash 启动一个bash终端，用于用户交互  
> 运行exit 退出容器  
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# docker run -it ubuntu:latest bash
root@59309e7b7593:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@59309e7b7593:/# exit
exit

//守护态运行，-d参数实现
docker run -d ubuntu /bin/sh 
```

### logs
查看容器输出
```
docker logs ce55432323
```


### pause
暂停运行中的容器
```
docker pause test
```

### stop
终止运行中的容器
```
docker stop test
```


### container prune
清除所有处于停止状态的容器
```
docker container prune
```

### restart
会将一个运行态的容器先终止，再重新启动
```
docker restart ce2342dsf
```

### exec
进入容器，如果启动容器时-d后台启动，进入容器通过exec命令
```
docker exec -it 123vdsa /bin/bash
```

### rm
删除容器
只能删除已处于终止或者退出状态的容器，不能删除正在运行的容器
如果直接删除一个运行中的容器，加-f参数强制删除
```
docker rm 12312sadfcas
```


### export
导出容器
导出一个已经创建的容器到一个文件，不管是否在运行
```
docker export -o test.tar cds32

//导出容器abc和容器cds到new.tar
docker export -o new.tar abc
docker export cds > new.tar
```

### import
上一步导出的文件导入变成镜像
import和load命令很像
Load导入镜像存储文件到本地镜像库
Import导入容器快照到本地镜像库
容器快照将丢失所有历史记录和元数据信息，仅保存容器当时的快照状态，所以小，镜像文件保存完整记录，体积更大
```
docker import new.tar - newubuntu:1.1
```

### ps
查看本地容器
-q 查看容器id
```
docker ps -a
//看到所有容器的 ID
docker ps -qa
```


### container inspect
查看容器具体信息
```
docker container inspect test
```

### top
查看容器内进程
类似linux的top命令，打印容器内的进程信息
```
docker top test
```

### container cp
支持在容器和主机之间复制文件
```
//将本机的data复制到test容器 /tmp路径
docker container cp data test:/tmp/
```

### container port
查看容器的端口映射情况
```
docker container port test
```

### container help
```
//查看docker支持的容器操作命令
docker container help
```


## 仓库
存放镜像点地方
和注册服务器（registry）区别:
注册服务器可以有多个仓库，每个仓库下可以用多个镜像
例：仓库地址private-docker.com/ubuntu 中，private-docker.com是注册服务器地址，ubuntu是仓库名


### 数据卷
### 容器端口映射和容器互联

### Dockerfile
