# Linux
## 常用命令
**1．cd命令**
（1）cd ~ 进入用户主目录
（2）cd - 返回进入此目录之前所在目录
（3）cd .. 返回上一级目录
（4）cd ../..返回上两级目录
（5）cd !$ 把上个命令的参数作为cd 参数使用

**2．ls命令**
-a all
-l 详细信息
-d 权限

**3．cp命令**
如果目标文件是目录，则会把源文件复制到该目录中；
如果目标文件也是普通文件，则会询问是否要覆盖它；
如果目标文件不存在，则执行正常的复制操作。
-r 参数递归持续复制（用于目录）
```
root@guxc ~:test guxc$ cp abc.txt 123.txt
root@guxc ~:test guxc$ ls
123.txt abc.txt
//拷贝到目录下
root@guxc ~:test guxc$ ls
1234.txt        cn-app-center   terraform-test  test
root@guxc ~:test guxc$ cp 1234.txt test
//拷贝到当前目录
root@guxc ~:test guxc$ ls
123.txt abc.txt
root@guxc ~:test guxc$ cp ../1234.txt .
root@guxc ~:test guxc$ ls
123.txt         1234.txt        abc.txt
//拷贝到上一级目录
root@guxc ~:test guxc$ cp 123.txt ..
root@guxc ~:test guxc$ ls
123.txt abc.txt
```

**4．mv命令**
Mv命令用于剪切或重命名文件,剪切操作不同于复制操作，因为它默认会把源文件删除，只保留剪切后的文件。如果在同一个目录中将某个文件剪切后还粘贴到当前目录下，其实也就是对该文件进行了重命名操作
```
//重命名
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# mv 123.txt 456.txt
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# ls
232.txt  456.txt 
```

**5．rm命令**
Rm命令用于删除文件或目录
在Linux系统中删除文件时，系统会默认向您询问是否要执行删除操作，
如果不想总是看到这种反复的确认信息，可在rm命令后跟上-f参数来强制删除。
另外，要想删除一个目录，需要在rm命令后面加一个-r参数才可以，否则删除不掉。

**6．touch命令**
Touch命令用于创建空白文件或设置文件的时间
```
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# touch 123.txt
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# cp 123.txt 232.txt
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# ls
123.txt  232.txt  applications  bin  cluster  config  global2.yaml  global.yaml  hopctl  hopctl.log  images  reports  system  test1.txt
```

**7．echo命令**
在终端设备上输出字符串或变量提取后的值
```
[root@guxc ~]# echo man
man
[root@guxc ~]# echo $SHELL
/bin/bash
```

**8．tar命令**
Tar命令用于对文件进行打包压缩或解压

> -c  
> 创建压缩文件  
> -x  
> 解开压缩文件  
> -t  
> 查看压缩包内有哪些文件  
> -z  
> 用Gzip压缩或解压  
> -j  
> 用bzip2压缩或解压  
> -v  
> 显示压缩或解压的过程  
> -f  
> 目标文件名  
> -p  
> 保留原始的权限与属性  
> -P  
> 使用绝对路径来压缩  
> -C  
> 指定解压到的目录  

```
[root@guxc test]# ls
test1.txt
//把test1.txt文件打包
[root@guxc test]# tar -czvf test1.tar.gz test1.txt
test1.txt
[root@guxc test]# ls
test1.tar.gz  test1.txt

//把test文件夹下文件打包
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# tar -czvf tt.tar.gz ./test
./test/
./test/test1.txt
./test/test1.tar.gz

//解压到当前目录
[root@guxc test2]# ls
tt.tar.gz
[root@guxc test2]# tar -xzvf tt.tar.gz
./test/
./test/test1.txt
./test/test1.tar.gz
[root@guxc test2]# ls
test  tt.tar.gz
[root@guxc test2]# cd test
[root@guxc test]# ls
test1.tar.gz  test1.txt

//解压到具体目录
[root@guxc test]# tar -xzvf test1.tar.gz -C /root/cnx-platform/adp-1.4.2
test1.txt
```


**9．ifconfig命令**
Ifconfig命令用于获取网卡配置与网络状态等信息，

**10．cat命令**
Cat -n
如果在查看文本内容时还想顺便显示行号的话，不妨在cat命令后面追加一个-n

**11．more命令**
More命令用于查看纯文本文件（内容较多的），需要阅读长篇小说或者非常长的配置文件

**head命令**
Head命令用于查看纯文本文件的前_N_行
```
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# head -n 10 global.yaml
clusterId: 495b127b-8472-47af-ba13-d5bb81f5527b
cluster:
  spec:
    masterGroups:
    - ipList:
      - 172.16.125.139
      env:
      - StorageDevice=/dev/vdb
      - KubeletRunDiskSize=100
      - DockerRunDiskSize=100
```

**tail命令**
Tail命令用于查看纯文本文件的后_N_行或持续刷新文件的最新内容,-f 能够持续刷新一个文件的内容
```
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# tail -n 10 global.yaml
system:
  CLUSTER_MASTER0_PUB_IP: 47.104.173.165
operator:
  cert: |
    -----BEGIN PUBLIC KEY-----
    MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDZGqjV46q9lA94ho3FYZl0KRag
    IEAZOoeQxiCXxqjLxDBxZ8qE/VN4FNH4CJOV7Zkp28JfdTIT+ANPw9dC3se+MDYe
    jKKEKsZkUcabWv8nKChu8Hbc7ojtjfL/U42F00nVaU6IpaI6C/YDQ1Pq76HUFLgW
    jaLE/KFaz9E7XRkVxwIDAQAB
    -----END PUBLIC KEY-----
```

**12．grep命令**
Grep命令用于按行提取文本内容

**13．ps命令**
Ps命令用于查看系统中的进程状态

**14．pstree命令**
Pstree命令用于以树状图的形式展示进程之间的关系，输入该命令后按回车键执行即可。

**13．diff命令**
Diff命令用于比较多个文件之间内容的差异，英文全称为“different”，语法格式为“diff [参数] 文件名称A 文件名称B”。
在使用diff命令时，不仅可以使用—brief参数来确认两个文件是否相同，还可以使用-c参数来详细比较出多个文件的差异之处。

```
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# diff -c global.yaml global2.yaml
*** global.yaml 2022-01-21 17:46:00.791628956 +0800
--- global2.yaml        2022-01-26 11:07:57.082868907 +0800
***************
*** 3,9 ****
    spec:
      masterGroups:
      - ipList:
!       - 172.16.125.139
        env:
        - StorageDevice=/dev/vdb
        - KubeletRunDiskSize=100
--- 3,9 ----
    spec:
      masterGroups:
      - ipList:
!       - 172.16.125.39
        env:
        - StorageDevice=/dev/vdb
        - KubeletRunDiskSize=100
```

**14．free命令**
Free命令用于显示当前系统中内存的使用量信息
```
[root@guxc ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:           7.6G        3.7G        328M        7.5M        3.7G        3.6G
```

**15．tr命令**
Tr命令用于替换文本内容中的字符，英文全称为“transform”，语法格式为“tr [原始字符] [目标字符]”。

**16．file命令**
File命令用于查看文件的类型

**17．date命令**
用于显示或设置系统的时间与日期
```
[root@guxc ~]# date
Tue Jan 25 17:25:46 CST 2022
[root@guxc ~]# date "+%Y-%m-%d"
2022-01-25
```

**18．reboot命令**
Reboot命令用于重启系统，输入该命令后按回车键执行即可。

**19．wget命令**
Wget命令用于在终端命令行中下载网络文件，英文全称为“web get”，语法格式为“wget [参数] 网址”。

**11．pidof命令**
Pidof命令用于查询某个指定服务进程的PID号码值。
```
[root@guxc ~]# pidof sshd
11628 2704
```

**12．kill命令**
Kill命令用于终止某个指定PID值的服务进程
接下来，使用kill命令把上面用pidof命令查询到的PID所代表的进程终止掉，其命令如下所示。这种操作的效果等同于强制停止sshd服务。
```
[root@guxc ~]# kill 2156
//但有时系统会提示进程无法被终止，此时可以加参数-9，表示最高级别地强制杀死进程
[root@guxc ~]# kill -9 2156
```

**13．killall命令**
Killall命令用于终止某个指定名称的服务所对应的全部进程
```
[root@linuxprobe ~]# pidof httpd
13581 13580 13579 13578 13577 13576
[root@linuxprobe ~]# killall httpd
[root@linuxprobe ~]# pidof httpd
```

**2．uname命令**
Uname命令用于查看系统内核版本与系统架构等信息
```
[root@guxc ~]# uname
Linux
```
**3．uptime命令**
Uptime命令用于查看系统的负载信息，输入该命令后按回车键执行即可。
Uptime命令真的很棒，它可以显示当前系统时间、系统已运行时间、启用终端数量以及平均负载值等信息。平均负载值指的是系统在最近1分钟、5分钟、15分钟内的压力情况（下面加粗的信息部分），负载值越低越好：
```
[root@guxc ~]# uptime
 19:17:33 up  1:45,  1 user,  load average: 8.30, 8.44, 8.73
```

**5．who命令**
Who命令用于查看当前登入主机的用户终端信息，输入该命令后按回车键执行即可。
这3个简单的字母可以快速显示出所有正在登录本机的用户名称以及他们正在开启的终端信息；如果有远程用户，还会显示出来访者的IP地址。
```
[root@guxc ~]# who
root     pts/0        2022-01-25 17:32 (100.104.30.247)
```

**6．last命令**
Last命令用于调取主机的被访记录
**8．tracepath命令**
Tracepath命令用于显示数据包到达目的主机时途中经过的所有路由信息
```
[root@guxc ~]# tracepath www.baidu.com
 1?: [LOCALHOST]                                         pmtu 1500
 1:  11.247.236.6                                          0.740ms 
 1:  10.90.187.6                                           0.928ms 
 2:  11.73.5.137                                           4.775ms 
```

**9．netstat命令**
Netstat命令用于显示如网络连接、路由表、接口状态等的网络相关信息
-I
显示网卡列表信息
-r
显示路由表信息

```
[root@guxc ~]# netstat -i
Kernel Interface table
Iface             MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
cali07fc6177437  1500 45550476      0      0 0      45550476      0      0      0 BMRU
cali11c18931171  1500   129431      0      0 0        136324      0      0      0 BMRU
cali525522c74e1  1500 45550476      0      0 0      45550476      0      0      0 BMRU
```
**11．sosreport命令**
Sosreport命令用于收集系统配置及架构信息并输出诊断文档

**uniq命令**
Uniq命令用于去除文本中连续的重复行
```
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# cat test1.txt
qwe
qwe
qwe
1234
345
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# uniq test1.txt
qwe
1234
345
```

**sort命令**
Sort命令用于对文本内容进行再排序
默认会按照字母顺序进行排序
```
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# cat test1.txt
qwe
1234
345
asd
bvcd
dfg
[root@guxc adp-1.4.2-beta.0-f03da9d-0e107b]# sort test1.txt
1234
345
asd
bvcd
dfg
qwe
```

**dd命令**
Dd命令用于按照指定大小和个数的数据块来复制文件或转换文件

**9．top命令**
Top命令用于动态地监视进程活动及系统负载等信息，输入该命令后按回车键执行即可。
前面介绍的命令都是静态地查看系统状态，不能实时滚动最新数据，而top命令能够动态地查看系统状态，因此完全可以将它看作是Linux中“强化版的Windows任务管理器”。

> 第1行：系统时间、运行时间、登录终端数、系统负载（3个数值分别为1分钟、5分钟、15分钟内的平均值，数值越小意味着负载越低）。  
> 第2行：进程总数、运行中的进程数、睡眠中的进程数、停止的进程数、僵死的进程数。  
> 第3行：用户占用资源百分比、系统内核占用资源百分比、改变过优先级的进程资源百分比、空闲的资源百分比等。其中数据均为CPU数据并以百分比格式显示，例如“99.9 id”意味着有99.9%的CPU处理器资源处于空闲。  
> 第4行：物理内存总量、内存空闲量、内存使用量、作为内核缓存的内存量。  
> 第5行：虚拟内存总量、虚拟内存空闲量、虚拟内存使用量、已被提前加载的内存量。  


## 重定向
```
//标准输出重定向（覆盖）
[root@guxc test]# echo "test1 is 1" > test.txt
[root@guxc test]# ls
test1.tar.gz  test1.txt  test.txt
[root@guxc test]# cat test.txt
test1 is 1
[root@guxc test]# echo "test1 is 2" > test.txt
[root@guxc test]# cat test.txt
test1 is 2

//标准输出重定向（非覆盖,追加）
[root@guxc test]# echo "test1 is 1" > test.txt
[root@guxc test]# cat test.txt
test1 is 1
[root@guxc test]# echo "test1 is 2" >> test.txt
[root@guxc test]# cat test.txt
test1 is 1
test1 is 2

//错误输出写入(覆盖，追加)
[root@guxc test]# cd as33df 2> test2.txt
[root@guxc test]# cat test2.txt
-bash: cd: as33df: No such file or directory
[root@guxc test]# cd as33dddf 2>> test2.txt
[root@guxc test]# cat test2.txt
-bash: cd: as33df: No such file or directory
-bash: cd: as33dddf: No such file or directory

//不区分标准输出和错误输出，只要命令有输出信息则全部追加写入到文件
[root@guxc test]# echo "123" &>> test2.txt
[root@guxc test]# cat test2.txt
-bash: cd: as33df: No such file or directory
-bash: cd: as33dddf: No such file or directory
123
[root@guxc test]# cd as33ddd333f &>> test2.txt
[root@guxc test]# cat test2.txt
-bash: cd: as33df: No such file or directory
-bash: cd: as33dddf: No such file or directory
123
-bash: cd: as33ddd333f: No such file or directory

```

## 管道
格式为“命令A | 命令B”
作用也可以用一句话概括为“**把前一个命令原本要输出到屏幕的信息当作后一个命令的标准输入**”

如果需要将管道符处理后的结果既输出到屏幕，又同时写入到文件中，则可以与tee命令结合使用。

Grep
**grep  在对一个或多个文件的内容进行基于模式的搜索的情况下是非常有用的。模式可以是单个字符、多个字符、单个单词、或者是一个句子**
```
//一个文件
[root@iZm5e8waxa372qgjp6rzo3Z test]# grep qwe main.txt
qwerfdesqwer234r
qwe

//多个文件
[root@iZm5e8waxa372qgjp6rzo3Z test]# grep qwe main.txt main2.txt
main.txt:qwerfdesqwer234r
main.txt:qwe
main2.txt:qwersdfghjKDsafd
main2.txt:gfdsaddvbfgqwer

//-l列出文件名
[root@iZm5e8waxa372qgjp6rzo3Z test]# grep -l qwe main.txt main2.txt
main.txt
main2.txt
```

Wc  显示行数
```
[root@iZm5e8waxa372qgjp6rzo3Z test]# wc -l main.txt
4 main.txt
```

主机名Hostname
```
//在/etc/hostname目录
[root@iZm5e8waxa372qgjp6rzo3Z test]# vi /etc/hostname
//查看
[root@iZm5e8waxa372qgjp6rzo3Z test]# hostname
iZm5e8waxa372qgjp6rzo3Z
```

## SHELL脚本
```
//脚本sh默认结尾，
//第一行#!/bin/bash指定shell解释器，
//第二行#test 123描述
[root@iZm5e8waxa372qgjp6rzo3Z test]# cat example.sh
#!/bin/bash
#test 123
pwd
ls -al

//执行脚本
[root@iZm5e8waxa372qgjp6rzo3Z test]# bash example.sh
/root/cnx-platform/adp-1.4.2-beta.0-8d1345a-206e6a/test
total 20
drwxr-xr-x  2 root root 4096 Jan 29 14:30 .
drwxr-xr-x 10 root root 4096 Jan 29 13:24 ..
-rw-r--r--  1 root root   33 Jan 29 14:30 example.sh
-rw-r--r--  1 root root   44 Jan 29 13:28 main2.txt
-rw-r--r--  1 root root   62 Jan 29 13:57 main.txt

//带参数
[root@iZm5e8waxa372qgjp6rzo3Z test]# cat parameter.sh
#!/bin/bash
echo "当前脚本名称$0"
echo "总共有$#个参数，分别为$*"
echo "第一个参数为$1,第三个参数为$3"
[root@iZm5e8waxa372qgjp6rzo3Z test]# bash parameter.sh one two three
当前脚本名称parameter.sh
总共有3个参数，分别为one two three
第一个参数为one,第三个参数为three
```

**管理员UID为0**：系统的管理员用户。
**系统用户UID为1～999**：Linux系统为了避免因某个服务程序出现漏洞而被黑客提权至整台服务器，默认服务程序会由独立的系统用户负责运行，进而有效控制被破坏范围。
**普通用户UID从1000开始**

**1.  id命令**
Id命令用于显示用户的详细信息
**2.  useradd命令**
Useradd命令用于创建新的用户账户
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# useradd guxc
[root@iZm5e8waxa372qgjp6rzo3Z ~]# id guxc
uid=1000(guxc) gid=1000(guxc) groups=1000(guxc)
```

**3.  groupadd命令**
Groupadd命令用于创建新的用户组
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# groupadd guxc
```
**4.  usermod命令**
Usermod命令用于修改用户的属性
```
//将用户guxc加入到root用户组中
[root@iZm5e8waxa372qgjp6rzo3Z ~]# usermod -G root guxc
[root@iZm5e8waxa372qgjp6rzo3Z ~]# id guxc
uid=1000(guxc) gid=1000(guxc) groups=1000(guxc),0(root)

//用-u参数修改linuxprobe用户的UID号码值
[root@iZm5e8waxa372qgjp6rzo3Z ~]# usermod -u 1234 guxc
[root@iZm5e8waxa372qgjp6rzo3Z ~]# id guxc
uid=1234(guxc) gid=1000(guxc) groups=1000(guxc),0(root)
```

**5.  passwd命令**
Passwd命令用于修改用户的密码、过期时间等信息
普通用户只能使用passwd命令修改自己的系统密码，而root管理员则有权限修改其他所有人的密码。
要修改自己的密码，只需要输入命令后敲击回车键即可：
```
[root@linuxprobe ~]# passwd
Changing password for user root.
New password: 此处输入密码值
Retype new password: 再次输入进行确认
passwd: all authentication tokens updated successfully.
```
要修改其他人的密码，则需要先检查当前是否为root管理员权限，然后在命令后指定要修改密码的那位用户的名称：
```
[root@linuxprobe ~]# passwd linuxprobe
Changing password for user linuxprobe.
New password:此处输入密码值
Retype new password: 再次输入进行确认
passwd: all authentication tokens updated successfully.
```

假设您有位同事正在度假，而且假期很长，那么可以使用passwd命令禁止该用户登录系统，等假期结束回归工作岗位时，再使用该命令允许用户登录系统，而不是将其删除。这样既保证了这段时间内系统的安全，也避免了频繁添加、删除用户带来的麻烦：
```
[root@linuxprobe ~]# passwd -l linuxprobe
Locking password for user linuxprobe.
passwd: Success
[root@linuxprobe ~]# passwd -S linuxprobe
linuxprobe LK 1969-12-31 0 99999 7 -1 (Password locked.)

```

在解锁时，记得也要使用管理员的身份；否则，如果普通用户也有锁定权限，系统肯定会乱成一锅粥：
```
[root@linuxprobe ~]# passwd -u linuxprobe
Unlocking password for user linuxprobe.
passwd: Success
[root@linuxprobe ~]# passwd -S linuxprobe
linuxprobe PS 1969-12-31 0 99999 7 -1 (Password set, SHA512 crypt.)
```

**6.  userdel命令**
Userdel命令用于删除已有的用户账户，英文全称为“user delete”，语法格式为“userdel [参数] 用户名”。
如果确认某位用户后续不会再登录到系统中，则可以通过userdel命令删除该用户的所有信息。在执行删除操作时，该用户的家目录默认会保留下来，此时可以使用-r参数将其删除。
```
[root@iZm5e8waxa372qgjp6rzo3Z ~]# userdel guxc
[root@iZm5e8waxa372qgjp6rzo3Z ~]# id guxc
id: guxc: no such user
```

## 文件权限与归属
文件的可读、可写、可执行权限的英文全称分别是read、write、execute，可以简写为r、w、x，亦可分别用数字4、2、1来表示
文件权限的数字表示法基于字符（rwx）的权限计算而来，其目的是简化权限的表示方式。例如，若某个文件的权限为7，则代表可读、可写、可执行（4+2+1）；若权限为6，则代表可读、可写（4+2）。
我们来看一个例子。现在有这样一个文件，其所有者拥有可读、可写、可执行的权限，其文件所属组拥有可读、可写的权限；其他人只有可读的权限。那么，这个文件的权限就是rwxrw-r—，数字法表示即为764。

rwx，rwx，rwx
421，421，421
文件所属者，文件所属组，其他用户

硬件配置 _dev_目录下
正常实体及其大概都是使用_dev_sd[a-]的磁盘文件名，虚拟机环境下，可能会使用_dev_vd[a-p]

参数可以用长格式（完整的选项名称），也可以用短格式（单个字母的缩写），两者分别用“—”与“-”作为前缀
man —help
Man -h

“[root@linuxprobe～]#”这部分内容是终端提示符，它用于向用户展示一些基本的信息—当前登录用户名为root，简要的主机名是linuxprobe，所在目录是～（这里的～是指用户家目录，第6章会讲解），#表示管理员身份（如果是$则表示普通用户，相应的权限也会小一些）

**Ctrl+c组合键**：终止当前进程的运行。
**Ctrl+d组合键**：键盘输入结束。
**Ctrl+l组合键**：清空当前终端中已有的内容（相当于清屏操作）

**.表示执行的意思，就是执行这个文件**
**._呢就表示执行当前目录下的某个文件，就比如当前目录有一个脚本a.sh，那么._a.sh就表示执行它**
**. 当前工作目录**

*./ 也是当前工作目录 不过一般这种写法后面都跟一个脚本文件 用来执行脚本


参考文档 [第2章 新手必须掌握的Linux命令 | 《Linux就该这么学》](https://www.linuxprobe.com/basic-learning-02.html)