---
title: Docker buildx
date: 2023-05-07 19:02:49
tags: Docker
categories: Docker
---

## docker buildx
docker build 的 --platform 参数只支持传递一个平台信息，如 --platform linux/arm64，也就是一次只能构建单个平台的镜像，如果需要支持多平台镜像，可以使用buildx
docker buildx是Docker的多架构构建工具，它可以帮助您在不同的处理器架构上进行跨平台构建，允许您使用单个命令构建多个平台的Docker镜像。
> 以下是使用docker buildx的一般步骤：
> 确保您已经安装了Docker，并且版本是19.03或更高版本。您可以通过运行docker version命令来验证。
> 确认是否已启用了experimental功能。docker buildx是一个实验性功能，需要将实验性功能设置为启用。您可以通过编辑~/.docker/config.json文件，将experimental设置为enabled，或者设置环境变量DOCKER_CLI_EXPERIMENTAL=enabled来启用它。


创建一个新的builder实例，使用docker buildx create命令创建一个新的构建器。例如，创建名为mybuilder的构建器：
```
docker buildx create --name mybuilder
```

切换到所创建的构建器中，使用docker buildx use命令。例如：
```
docker buildx use mybuilder
```

配置构建器以支持多个平台。您可以使用docker buildx inspect命令来查看当前构建器的配置，并使用docker buildx create命令的--platform选项来添加支持的平台。例如，为了支持Linux的ARM和x86_64架构
inspect 子命令用来检查构建器状态，使用 --bootstrap 参数则可以启动 mybuilder 构建器。
```
docker buildx inspect --bootstrap
docker buildx create --use --platform linux/amd64,linux/arm64
```

执行docker buildx ls查看builder列表
```
$ docker buildx ls
NAME/NODE     DRIVER/ENDPOINT  STATUS  BUILDKIT     PLATFORMS
mybuilder *   docker-container                      
  mybuilder0  colima           running v0.12.1      linux/arm64, linux/amd64, linux/amd64/v2
colima        docker                                
  colima      colima           running v0.8+unknown linux/arm64, linux/amd64
default                        error                
desktop-linux                  error                
```
其中 PLATFORMS 一列所展示的值linux/arm64, linux/amd64, linux/amd64/v2就是当前构建器所支持的所有平台了

docker ps 命令可以看到 mybuilder 构建器所对应的 BuildKit 容器已经启动。
```
guxc@guxuchengdeMacBook-Pro yourenMapper % docker ps                                                                            
CONTAINER ID   IMAGE                           COMMAND       CREATED          STATUS          PORTS     NAMES
c1f0509bfb57   moby/buildkit:buildx-stable-1   "buildkitd"   42 minutes ago   Up 42 minutes             buildx_buildkit_mybuilder0
```

构建跨平台的Docker镜像。您可以使用docker buildx build命令来构建镜像，与普通的docker build命令类似。唯一的区别是，您需要使用--builder选项指定构建器的名称。例如：
```
docker buildx build --builder mybuilder --platform linux/amd64,linux/arm64 -t your-image:tag . --load/--push
```
上述命令将会构建一个同时支持x86_64和ARM架构的镜像。
--load构建镜像文件，并导出到本地Docker images中。之后的保存、启动可以直接用docker指令
--push推送到仓库中

## 案例
以一个 Go 程序为例，来演示如何构建跨平台镜像
```
hello.go 程序如下：

package main

import (
    "fmt"
    "runtime"
)

func main() {
    fmt.Printf("Hello, %s/%s!\n", runtime.GOOS, runtime.GOARCH)
}
```
Dockerfile 内容如下：
```
FROM golang:1.20-alpine AS builder
WORKDIR /app
ADD . .
RUN go build -o hello .

FROM alpine:latest
WORKDIR /app
COPY --from=builder /app/hello .
CMD ["./hello"]
```
这是一个普通的 Dockerfile 文件，为了减小镜像大小，使用了多阶段构建。它跟构建仅支持当前平台的镜像所使用的 Dockerfile 没什么两样。
使用 docker buildx 来构建跨平台镜像了。
```
$ docker buildx build --platform linux/arm64,linux/amd64 -t hello-go .
```
使用 --push 可以将镜像推送到 Docker Hub 远程仓库，使用 --load 可以将镜像保存在本地。

