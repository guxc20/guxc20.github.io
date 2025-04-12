---
title: Hexo+github+cloudflare搭建博客
date: 2023-12-19 19:45:49
tags: [其他]
categories: 其他
---


## 安装Node.js
[https://nodejs.cn/download/](https://nodejs.cn/download/)

一路默认next

安装后

```plain
node -v
npm -v
```

如果没有梯子的话，可以使用国内镜像进行加速。

```plain
sudo chown -R guxc:staff "/Users/guxc/.npm"
npm install -g cnpm --registry=https://registry.npmmirror.com
```

## 安装Hexo
```plain
mkdir hexo
cd hexo
cnpm install -g hexo-cli
mkdir hexo-test
hexo init hexo-test
cd hexo-test
sudo cnpm install
hexo new test
hexo s
```

## 架构
```plain
├── _config.landscape.yml：主题配置文件，如果

├── _config.yml：站点配置文件，对站点进行配置。

├── node_modules：用来存储已安装的各类依赖包。

├── package-lock.json：包版本依赖锁文件。

├── package.json：应用数据，Hexo的版本以及相关依赖包的版本等。

├── scaffolds：博客模版文件夹，包含page.md，post.md，draft.md三种。

├── source：资源文件夹，存放静态资源如博客md文件、图片等。

└── themes：主题文件夹，Hexo通过将网站内容与主题组合来生成静态网站。
```



## 配置主题
```plain
 sudo cnpm install --save hexo-theme-fluid
 hexo new page about
```

如果出现报错

```plain
Error: Cannot find module 'hexo-pagination'
```

执行

```plain
sudo cnpm install hexo-pagination --save
```



修改_config.yml

```plain
theme: fluid  # 指定主题

language: zh-CN  # 指定语言，会影响主题显示的语言，按需修改
```

修改/source/about/index.md

```plain
---
title: 标题
layout: about
---

这里写关于页的正文，支持 Markdown, HTML
```



Fluid：[https://hexo.fluid-dev.com/docs/guide/](https://hexo.fluid-dev.com/docs/guide/)



## 书写
```plain
# Site
title: 老表的博客
subtitle: '爱思思爱猫爱技术'
description: '记录老表生活、Python和Go学习笔记，由Hexo+Github搭建。'
keywords: 生活,养猫,Python,Go,数据分析,Web开发
author: 老表
language: zh-CN
timezone: 'Asia/Shanghai'
```

## 废弃部署方式（国内太慢）
```plain
deploy:
  - type: git
    repository:
      github: git@github.com:guxc20/guxc20.github.io.git
    branch: main

```

部署到 GitHub，需要安装相关工具 hexo-deployer-git。

```plain
sudo cnpm install hexo-deployer-git --save
git init
```

接下来直接输入指令配置就行了，hexo clean 清除缓存数据，hexo g生成相关静态文件。

```plain
hexo clean 
hexo g
```

### 添加域名
Custom domain

添加域名blog.guxc.cn

在本地项目中的 blog/source 文件夹下新建一个CNAME文件

```plain
vi source/CNAME
添加blog.guxc.cn

重新和部署项目
hexo g
hexo d
```

直接浏览器访问blog.guxc.cn



## 新部署方式（使用cloudflare加速）
hexo d上传github的只是public的内容，如果想要把所有内容保存到github

```plain
git co -b hexo
git add .
git ci -m hexo-blog
git remote -v
git remote add origin git@github.com:guxc20/guxc20.github.io.git
git remote -v                                                 
git push -f origin hexo                                       
```

### cloudflare
主页 --> workers和pages --> 新建pages

选择github仓库 hexo分支

构建配置命令这里一定要输入 npx hexo generate

配置环境变量 NODEVERSION 和 NPMVERSION

```plain
node -v
npm -v
```

部署成功，可以通过[https://guxc20-github-io.pages.dev/](https://guxc20-github-io.pages.dev/) 访问

cloudflare的自定义域 --> 添加域名

阿里云域名 配置cname指向[https://guxc20-github-io.pages.dev/](https://guxc20-github-io.pages.dev/)，

然后可以通过[https://blog.guxc.cn/](https://blog.guxc.cn/)访问了

## 常用命令
```plain
## 本地启动项目，s 表示 server
hexo s

## 创建一个新的博客，n 表示 new
hexo n "first blog"

## 生成静态文件，g 表示 generate
hexo g

## 部署 Hexo 网站，d 表示 deploy
hexo d

## 清除缓存文件 (db.json) 和已生成的静态文件 (public)
hexo clean
```

<font style="color:rgb(48, 48, 48);"></font>

## 参考
[https://hexo.io/zh-cn/docs/commands#deploy](https://hexo.io/zh-cn/docs/commands#deploy)

[https://hexo.fluid-dev.com/docs/start/#%E4%B8%BB%E9%A2%98%E7%AE%80%E4%BB%8B](https://hexo.fluid-dev.com/docs/start/#%E4%B8%BB%E9%A2%98%E7%AE%80%E4%BB%8B)

[https://xie.infoq.cn/article/90dc08c57463df3a1b2ad960f](https://xie.infoq.cn/article/90dc08c57463df3a1b2ad960f)

[https://godweiyang.com/2018/04/13/hexo-blog/#toc-heading-11](https://godweiyang.com/2018/04/13/hexo-blog/#toc-heading-11)

[https://www.julydate.com/post/60859300/](https://www.julydate.com/post/60859300/)

[https://tclx.top/posts/6a7c538b/](https://tclx.top/posts/6a7c538b/)

[https://zhuanlan.zhihu.com/p/685078577](https://zhuanlan.zhihu.com/p/685078577)

