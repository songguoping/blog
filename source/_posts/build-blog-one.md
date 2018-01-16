---
title: 使用 hexo-theme-next 搭建博客（一）
date: 2017-12-13 20:30:13
description: 本系列文章旨在教会你通过 next 主题搭建自己的 hexo 博客，本章节主要介绍 Hexo 的本地站点的搭建以及使用 next 主题
tags: 
    Hexo
---
<p id="div-border-top-blue">
本系列文章旨在教会你通过 next 主题搭建自己的 hexo 博客，跟着本系列完成后你的博客将会具备以下几个功能：
- 强调精于心，简于形的 next 主题，也可以很方便的定制和扩展；
- Hexo 同时部署在 Coding 和 GitHub 上并使用 DNSPod 分流;
- LeanCloud 为文章添加阅读量统计功能，并在首页可以显示，不蒜子实现站内 UV PV 等统计；
- 百度统计；
- 站内搜索功能；
- RSS;
- 代码高亮，社交链接，打赏，动画等功能；
- 评论系统，可以匿名评论；
- 备份博客在 Github 上；
- 同步更新博客；
- ......
</p>

## 软件环境
* Mac OS
* Git
* Node.js

## 本地站点搭建
### 安装 Git
> Windows：下载安装 git 。<a id="download" href="https://git-scm.com/download/win"><i class="fa fa-download"></i><span> Download git</span></a>
> Mac：使用 [Homebrew](http://mxcl.github.com/homebrew/)，[MacPorts](http://www.macports.org/) 或下载 [安装程序](http://sourceforge.net/projects/git-osx-installer/) 安装

### 安装 Node.js
下载安装 Node.js。<a id="download" href="https://nodejs.org/en/#download"><i class="fa fa-download"></i><span> Download Node.js</span></a>,点击安装各个系统的版本，无脑的下一步即可完成安装。node.js 安装完成后，npm 也被安装完成。检测是否安装成功可以在终端输入命令查看 node.js 和 npm 的版本号。
如下图所示：
![node_version](http://p0ufe2pi4.bkt.clouddn.com/build-blog-one/node_version.png)

### 安装 Hexo
所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo。
```bash
$ npm install -g hexo-cli
```
输入以下命令查看 Hexo 的版本号：
```bash
$ hexo -v
```
如下图所示：
![hexo_version](http://p0ufe2pi4.bkt.clouddn.com/build-blog-one/hexo_version.png)
### 建站
安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件并拉取所需要的依赖。
```bash
$ cd <folder>
$ hexo init 
$ npm install
```
如下图所示：
![hexo_init](http://p0ufe2pi4.bkt.clouddn.com/build-blog-one/hexo_init.png)
新建完成后，指定文件夹的目录如下：
```bash
.
├── _config.yml # 网站的配置信息，在此称为站点配置文件，往后主题目录下也有相同命名的文件，称为主题配置文件
├── package.json # 应用程序的信息
├── scaffolds # 模版 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。
├── source # 资源文件夹是存放用户资源的地方。
|   ├── _drafts
|   └── _posts
└── themes # 主题 文件夹。Hexo 会根据主题来生成静态页面。
```
执行以下命令，生成静态页面：
```bash
hexo generate
```
执行以下命令，启动服务：
```bash
hexo server
```
如下图所示：
![hexo_server](http://p0ufe2pi4.bkt.clouddn.com/build-blog-one/hexo_server.png)
打开浏览器，输入 [http://localhost:4000/](http://localhost:4000/)，即可在本地查看基于 Hexo 的默认主题的原型：
![hexo_index](http://p0ufe2pi4.bkt.clouddn.com/build-blog-one/hexo_index.png)
### 安装 NexT
Hexo 安装主题的方式非常简单，只需要将主题文件拷贝至站点目录的 themes 目录下， 然后修改下配置文件即可。具体到 NexT 来说，安装步骤如下。
在终端窗口下，定位到 Hexo 站点目录下。使用 `git checkout` 代码：
```bash
$ cd your-hexo-site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```
如下图所示：
![next_clone](http://p0ufe2pi4.bkt.clouddn.com/build-blog-one/git_clone_next.png)
当 克隆/下载 完成后，打开 <span id="inline-blue">站点配置文件</span>， 找到 `theme` 字段，并将其值更改为 `next`。
```bash
theme: next
```
启动 Hexo 本地站点，并开启调试模式（即加上 --debug），整个命令是 `hexo s --debug`，在启动之前，我们最好使用 `hexo clean` 来清除 Hexo 的缓存。
当你看到站点的外观与下图所示类似时即说明你已成功安装 NexT 主题。这是 NexT 默认的 Scheme —— Muse
![next_muse](http://p0ufe2pi4.bkt.clouddn.com/build-blog-one/hexo_index.png)
## 总结
本章节主要介绍 Hexo 的本地站点的搭建以及使用 next 主题，[下一章节](/2017-12-14/build-blog-two.html)主要介绍把博客部署在 Github page 和 Coding Page 上，并配置自己的域名访问。
### Hexo 基本指令
```bash
$ hexo init # 新建一个网站
$ hexo new [layout] <title> # 新建一篇文章
$ hexo generate # 生成静态文件，部署时可以使用 hexo g -d
$ hexo publish [layout] <filename> # 发表草稿
$ hexo server # 启动服务器
$ hexo deploy # 部署网站
$ hexo clean # 清除缓存文件 (db.json) 和已生成的静态文件 (public)
```

### 简写命令
```bash
$ hexo new == hexo n
$ hexo generate == hexo g
$ hexo server == hexo s
$ hexo deploy == hexo d
```

### 常见问题
* 注意在配置文件中，冒号后面要留一个空格，要不然配置不生效

## 参考
>hexo doc: https://hexo.io/zh-cn/docs/
>Never_yu's Blog: https://neveryu.github.io/2016/09/03/hexo-next-one/

    
    
    
    





