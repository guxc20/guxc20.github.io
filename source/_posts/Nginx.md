---
title: Nginx
date: 2023-10-15 20:43:09
tags: [中间件, nginx]
categories: 中间件
---

## 安装
安装nginx软件所需的依赖插件
```bash
yum install -y gcc-c++
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
yum install -y openssl openssl-devel
```
下载nginx包
```bash
wget http://nginx.org/download/nginx-1.20.2.tar.gz 
```
解压
```bash

# 解压到当前目录下
[root@master ~]# tar -zxvf nginx-1.20.2.tar.gz
[root@master ~]# ls
nginx-1.20.2  nginx-1.20.2.tar.gz  
[root@master ~]# cd nginx-1.20.2
[root@master nginx-1.20.2]# ls
auto  CHANGES  CHANGES.ru  conf  configure  contrib  html  LICENSE  man  README  src
[root@master nginx-1.20.2]# pwd
/root/nginx-1.20.2
```
创建一个目录作为nginx的安装目录，设置nginx的安装路径为上面/usr/nginx  
```bash
[root@master nginx-1.20.2]# mkdir /usr/nginx
[root@master nginx-1.20.2]# ./configure --prefix=/usr/nginx
```
然后可以看到我们的nginx目录下，多了一个Makefile文件
```bash
[root@master nginx-1.20.2]# ls
auto  CHANGES  CHANGES.ru  conf  configure  contrib  html  LICENSE  Makefile  man  objs  README  src
```
安装
```bash
#编译安装nginx
[root@master nginx-1.20.2]# make & make install
```
进入到安装后的目录下
```bash
[root@master nginx-1.20.2]# cd /usr/nginx/

## conf: 配置目录
## html: 放置静态资源的。
## logs: 日志
## sbin: 启动和关闭nginx的脚本
[root@master nginx]# ls
conf  html  logs  sbin
```
进入sbin目录下，执行一下命令启动
```
[root@master nginx]# cd sbin/
[root@master sbin]# ls
nginx
[root@master sbin]# ./nginx


/nginx 开启nginx
./nginx -s stop 关闭nginx
./nginx -s reload 重新加载配置文件---修改完配置文件后使用该命令
```
访问
![](../images/nginx1.png)

如果访问不通，可能防火墙未关闭或者你的端口号未选择放行
```
设置防火墙允许放行哪些端口号
firewall-cmd --add-port=你的端口号/tcp --zone=public --permanent

放行端口号后需要重启防火墙:
systemctl restart firewalld

查看哪些端口号被放行 
firewall-cmd --list-port 

关闭防火墙----慎重【关闭】 开启还会启动
systemctl stop firewalld 

```


## Docker启动
```
$ docker run --name my-nginx -p 8080:80
 -v /host/path/nginx.conf:/etc/nginx/nginx.conf -d nginx

```
```
user  nginx;
worker_processes  1;

error_log /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    server {
        listen 80;

        location / {
            proxy_pass http://192.168.159.181:30028;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}

```
$host、$remote_addr 和其他 proxy_set_header 指令用于将原始请求的相关信息传递给后端服务。

## 配置文件
```
cd /usr/nginx/conf
目录下的nginx.conf文件
```
查看文件
```
worker_processes 8;#Nginx 进程数，建议按照CPU数目来指定，一般为它的倍数 (如,2个四核的CPU计为8)。worker_rlimit_nofile 65535;  #一个Nginx 进程打开的最多文件描述符数目
worker_connections 65535;#每个进程允许的最多连接数

server {
        #监听80端口，80端口是知名端口号，用于HTTP协议
        listen       80;

        #定义使用www.xx.com访问
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;    //默认访问index
        }
}
```
### 修改index
```
[root@master html]# pwd
/usr/nginx/html
[root@master html]# ls
50x.html  index.html
[root@master html]# vi index.html
[root@master html]# cat index.html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome gxc to nginx!</h1>				//修改添加gxc
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
重新加载
```
./nginx -s reload
```
访问，发现index页面已修改
![](../images/nginx2.png)
### 访问我的页面
修改config
```
 server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   gxc;
            index  gxc.html;
        }
}
```
创建页面
```
[root@master nginx]# pwd
/usr/nginx
[root@master nginx]# mkdir gxc
[root@master nginx]# vi gxc.html
```
访问
![](../images/nginx3.png)
## 反向代理
**正向代理是代理客户端，反向代理是代理服务器。**
正向代理：翻墙业务
反向代理: 代理的为服务器端，对于客户来说不知道访问的真实服务器。
```
  server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass http://10.10.101.49:8080/ping;
        }
}
```
访问的程序
```
func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080
}
```
访问
![](../images/nginx4.png)

## 负载均衡
10.10.101.49机器上运行程序
```
func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "p1",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080
}

```
10.10.101.50机器上运行程序
```
func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "p2",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080
}

```
### 轮询
修改配置文件
```
 upstream test1 {
        server 10.10.101.49:8080;
        server 10.10.101.50:8080;
    }
    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass http://test1/ping;
        }
}
```
访问，发现轮训
```
guxc@guxuchengdeMacBook-Pro ~ % curl http://10.10.101.49/
{"message":"p2"}%
guxc@guxuchengdeMacBook-Pro ~ % curl http://10.10.101.49/
{"message":"p1"}%
guxc@guxuchengdeMacBook-Pro ~ % curl http://10.10.101.49/
{"message":"p2"}%
guxc@guxuchengdeMacBook-Pro ~ % curl http://10.10.101.49/
{"message":"p1"}%
```
### 权重
修改配置文件
```
 upstream test1 {
        server 10.10.101.49:8080 weight=1;
        server 10.10.101.50:8080 weight=2;
    }
server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass http://test1/ping;
        }
}
```
访问
```
guxc@guxuchengdeMacBook-Pro ~ % curl http://10.10.101.49/
{"message":"p2"}%
guxc@guxuchengdeMacBook-Pro ~ % curl http://10.10.101.49/
{"message":"p1"}%
guxc@guxuchengdeMacBook-Pro ~ % curl http://10.10.101.49/
{"message":"p2"}%
guxc@guxuchengdeMacBook-Pro ~ % curl http://10.10.101.49/
{"message":"p2"}%
guxc@guxuchengdeMacBook-Pro ~ % curl http://10.10.101.49/
{"message":"p1"}%
```
### ip_hash
每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。
```
 upstream test1 {
        server 10.10.101.49:8080;
        server 10.10.101.50:8080;
      	ip_hash
    }
```
## 动静分离
原来静态文件，动态文件都存放在tomcat，用户发送一个请求，请求静态资源，需要再渲染，多次请求tomcat，增加压力
现在把静态文件放在nginx，动态文件放在tomcat，这样减少tomcat压力
> 将springboot项目中的static文件提取出来，将不带static文件的项目打包成jar，放到linux中（一般都放在 /project中），然后将static文件放到nginx的根目录下（/usr/nginx/）。然后修改我们的配置文件：
> 配置了一个server：

```
server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass http://test1/ping;
        }
        location ~ \.jpg|.png|.css|.js$ {
            # 在nginx目录下放static目录，其他路径需要写绝对路径，nginx目录下可以使用相对路径
            root static
        }
}
```
## 高可用
![](../images/nginx5.png)

我们的Nginx虽然承受并发的能力非常强，但不排除由意外情况导致我们的nginx会宕机。为了尽可能使我们的项目正常运行，这时我们可以启用备胎计划，也就是再搞一台服务器当做代理服务器，这两台代理服务器之间有一个主从关系。平常我们的备胎代理服务器不会使用，当标记为MASTER的主代理服务器宕机了，我们的被标记为BACKUP的从代理服务器就可以成功上位，充当MASTER的位置实现代理功能，当MASTER的代理服务器正常运行以后，BACKYUP就退居二线，不再使用。
keepalived可以虚拟一个ip

## 代理文件目录
修改nginx.conf
```
http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65;


    server {
        listen       80;
        server_name  localhost;
        access_log  logs/access.log;

        location /gxc_dir/ {
            alias /var/www/;
        }
    }
}
```
然后执行
```
sudo /usr/local/nginx/sbin/nginx  -s reload
```
在/var/www目录下放置文件
```
[root@edgestack-master www]# pwd
/var/www
[root@edgestack-master www]# ls
see.json
[root@edgestack-master www]# cat see.json
{
"fdd":"33"
}
```
浏览器访问[http://10.10.101.159/gxc_dir/see.json](http://10.10.101.159/gxc_dir/see.json)
可以看到
![](../images/nginx6.png)

扩展
```
	location /mylog {
		  autoindex on;
      charset utf-8;						# 文件编码
		  autoindex_exact_size off; # 显示出⽂件的⼤概⼤⼩
		  autoindex_localtime off;  # 显示的⽂件时间
		  auth_basic "Auth access Blog Input your Passwd!";   #访问时输入密码
	    auth_basic_user_file /usr/local/mdtassistant/nginx/users;
      alias /usr/local/mdtassistant/version;
   }

```

## 不同网段服务代理
集群部署在159网段，办公网是145网段，想要在145网段访问159上的服务，使用nginx代理
集群的master节点有159网段和145网段的两张网卡
```
docker run --name my-nginx -p 8088:80 -v /home/nginx.conf:/etc/nginx/nginx.conf -d nginx 
```
访问图片
[http://192.168.145.101:8088/picture/demo/kxt134_rtsp_person_True_2024-03-18](http://192.168.145.101:8088/picture/demo/kxt134_rtsp_person_True_2024-03-18%2007%3A58%3A19_otb5Q.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=admin%2F20240321%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240321T145153Z&X-Amz-Expires=604800&X-Amz-SignedHeaders=host&X-Amz-Signature=7b9ed4e18899bd49904770cda50371ad059465605b259e163880b5b78a6e70a7 )
报错
SignatureDoesNotMatch
原因
是签名不正确,和nginx反向代理在做转发的时候所携带的header有关系
解决方法
```
user  nginx;
worker_processes  1;

error_log /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    server {
        listen 80;

        location /picture/ {
            proxy_pass http://192.168.159.181:30010/;
            client_max_body_size 1024m;
            proxy_http_version 1.1;
            proxy_set_header Host 192.168.159.181:30010; 
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Real-PORT $remote_port;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Forwarded-Host $http_host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        location / {
            proxy_pass http://192.168.159.181:30028;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}

```
许多文档写了  proxy_set_header Host $http_host;
这个对于转发不同ip的服务不正确，这里直接将proxy_set_header Host 192.168.159.181:30010; 指定为minio的地址，就成功了

## 细节
路径加 / 如何区分
如果proxy_pass末尾有斜杠/，proxy_pass不拼接location的路径
如果proxy_pass末尾无斜杠/，proxy_pass会拼接location的路径

1、proxy_pass末尾有斜杠
```
location  /api/ {
    proxy_pass http://127.0.0.1:8000/;
}
```
请求地址：[http://localhost/api/test](http://localhost/api/test)
转发地址：[http://127.0.0.1:8000/test](http://127.0.0.1:8000/test)

2、proxy_pass末尾无斜杠
```
location  /api/ {
    proxy_pass http://127.0.0.1:8000;
}
```
请求地址：[http://localhost/api/test](http://localhost/api/test)
转发地址：[http://127.0.0.1:8000/api/test](http://127.0.0.1:8000/api/test)

3、proxy_pass包含路径，且末尾有斜杠
```
location  /api/ {
    proxy_pass http://127.0.0.1:8000/user/;
}
```
请求地址：[http://localhost/api/test](http://localhost/api/test)
转发地址：[http://127.0.0.1:8000/user/test](http://127.0.0.1:8000/user/test)

4、proxy_pass包含路径，末尾无斜杠
```
location  /api/ {
    proxy_pass http://127.0.0.1:8000/user;
}
```
请求地址：[http://localhost/api/test](http://localhost/api/test)
转发地址：[http://127.0.0.1:8000/usertest](http://127.0.0.1:8000/usertest)

四、文件路径 alias与root区别
root的处理结果是：root路径＋location路径
alias的处理结果是：使用alias路径替换location路径

