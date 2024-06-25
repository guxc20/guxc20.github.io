---
title: Makefile
date: 2023-12-16 17:43:09
tags: [脚本, makefile]
categories: 脚本
---

## 规则
### 格式
```bash
<target> : <prerequisites> 
[tab]  <commands>

目标：前置条件
	命令
```
### 目标 target
目标必须，前置条件和命令可选，必须存在一个
例子：
```bash
# 假设文件 a.txt 依赖于 b.txt 和 c.txt ，是后面两个文件连接（cat命令）的产物
a.txt: b.txt c.txt
    cat b.txt c.txt > a.txt
```
目标通常是文件名，也可以是某个操作名称，这种称为“伪目标”，例如
```bash
clean:
	rm *.o

$ make clean
```
如果目录存在文件叫clean，则该命令不会执行，因为makefile发现clean文件存在，就认为没有必要构建了，所以一般需要声明phony
```bash
clean:
	rm *.o
.PHONY: clean
```
声明后，就不会去找clean文件了
### 前置条件 prerequisities
通常是一组文件名，空格分隔。
目标是否重新构建的判断标准：只要一个前置文件不存在，或者前置文件的last时间戳比目标新，目标就需要构建
```bash
result.txt: source.txt
    cp source.txt result.txt

source.txt:
    echo "this is the source" > source.txt

## 没文件就会构建，有了就不会重新生成了
$ make result.txt
```
### 命令 commands
由一行或者多行Shell命令组成
每行命令在一个单独的shell中执行。这些Shell之间没有继承关系。
```bash
var-lost:
    export foo=bar
    echo "foo=[$$foo]"
```
上面代码执行后（make var-lost），取不到foo的值。因为两行命令在两个不同的进程执行。一个解决办法是将两行命令写在一行，中间用分号分隔，在换行符前加反斜杠转义。
```bash
var-kept:
    export foo=bar; echo "foo=[$$foo]"
    
或者在换行符前加反斜杠转义。

var-kept:
    export foo=bar; \
    echo "foo=[$$foo]"
```
## 语法
### 注释
```bash
# 这是注释
result.txt: source.txt
    # 这是注释
    cp source.txt result.txt # 这也是注释
```
### 回声
正常情况下，make会打印每条命令，然后再执行，这就叫做回声（echoing）。
由于在构建过程中，需要了解当前在执行哪条命令，所以通常只在注释和纯显示的echo命令前面加上@。
在命令的前面加上@，就可以关闭回声。
```bash
test2:
    echo test11
    @echo TOD


$ make test2
echo test11
test11
TOD
```
### 通配符
Makefile 的通配符与 Bash 一致，主要有星号（*）、问号（？）和 [...] 。比如， *.o 表示所有后缀名为o的文件。
```bash
clean:
	rm -f *.o
```
### 模式匹配
主要用到的匹配符是%。比如，假定当前目录下有 f1.c 和 f2.c 两个源码文件，需要将它们编译为对应的对象文件。
```bash
%.o: %.c

等于

f1.o: f1.c
f2.o: f2.c
```
### 变量和赋值
```bash
txt = Hello World
test:
    @echo $(txt)
```
变量需要放在 $( ) 之中
调用Shell变量，需要在美元符号前，再加一个美元符号
```bash
test:
    @echo $$HOME
```
四个赋值运算符 （=、:=、？=、+=）区别
```bash
VARIABLE = value
# 在执行时扩展，允许递归扩展。

VARIABLE := value
# 在定义时扩展。

VARIABLE ?= value
# 只有在该变量为空时才设置值。

VARIABLE += value
# 将值追加到变量的尾端。
```
### 内置变量
Make命令提供一系列内置变量，比如，$(CC) 指向当前使用的编译器，$(MAKE) 指向当前使用的Make工具。
```bash
output:
    $(CC) -o output input.c

等于
output:
    gcc+ -o output input.c
```
### 自动变量
#### $@
$@指代当前目标，就是Make命令当前构建的那个目标。比如，make foo的 $@ 就指代foo。
```bash
a.txt b.txt: 
    touch $@

等于
a.txt:
    touch a.txt
b.txt:
    touch b.txt


test2:
	@echo $@

 $ make test2
 test2
```
#### $< 
$< 指代第一个前置条件。比如，规则为 t: p1 p2，那么$< 就指代p1。
```bash
a.txt: b.txt c.txt
    cp $< $@ 

等于

a.txt: b.txt c.txt
    cp b.txt a.txt 
```
#### $?
$? 指代比目标更新的所有前置条件，之间以空格分隔。比如，规则为 t: p1 p2，其中 p2 的时间戳比 t 新，$?就指代p2。
#### $^
$^ 指代所有前置条件，之间以空格分隔。比如，规则为 t: p1 p2，那么 $^ 就指代 p1 p2 。
#### $*
$* 指代匹配符 % 匹配的部分， 比如% 匹配 f1.txt 中的f1 ，$* 就表示 f1。
### 判断和循环
判断
```bash
ifeq ($(CC),gcc)
  libs=$(libs_for_gcc)
else
  libs=$(normal_libs)
endif
```
循环
```bash
LIST = one two three
all:
    for i in $(LIST); do \
        echo $$i; \
    done

# 等同于

all:
    for i in one two three; do \
        echo $i; \
    done
```
### 函数
```bash
$(function arguments)
# 或者
${function arguments}
```
#### 内置函数
##### subst   文本替换
```bash
$(subst from,to,text)
```
例如：将字符串"feet on the street"替换成"fEEt on the strEEt"。
```bash
$(subst ee,EE,feet on the street)
```
##### patsubst 模式匹配替换
```bash
$(patsubst pattern,replacement,text)
```
例子：将文件名"x.c.c bar.c"，替换成"x.c.o bar.o"。
```bash
$(patsubst %.c,%.o,x.c.c bar.c)
```
##### shell 用来执行shell命令
```bash
srcfiles := $(shell echo src/{00..99}.txt)
```
## 参考
[https://www.ruanyifeng.com/blog/2015/02/make.html](https://www.ruanyifeng.com/blog/2015/02/make.html)

