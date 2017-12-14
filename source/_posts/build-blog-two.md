---
title: 使用 hexo-theme-next 搭建博客（二）
date: 2017-12-14 22:40:51
description: 本章节主要介绍把博客部署在 GitHub page 和 Coding Page 上，并配置自己的域名访问
tags: 
    Hexo
---
<p id="div-border-top-blue">
本系列文章旨在教会你通过 next 主题搭建自己的 hexo 博客，跟着本系列完成后你的博客将会具备以下几个功能：
- 强调精于心，简于形的 next 主题，也可以很方便的定制和扩展；
- Hexo同时部署在 Coding 和 GitHub 上并使用 DNSPod 分流;
- LeanCloud 为文章添加阅读量统计功能，并在首页可以显示，不蒜子实现站内 UV PV 等统计；
- 百度统计；
- 站内搜索功能；
- RSS;
- 代码高亮，社交链接，打赏，动画等功能；
- 评论系统，可以匿名评论；
- ......
</p>

[上一篇文章](/2017-12-13/build-blog-one.html)我们主要介绍 Hexo 的本地站点的搭建以及使用 next 主题，本章节主要介绍把博客部署在 `GitHub page` 和 `Coding Page` 上，并配置自己的域名访问。

## 部署到 GitHub 
### 创建 Repository
登录 GitHub 账号后，在首页右上角会有 New repository 按钮，点击后进入如下界面：
![github_new_repository](http://p0ufe2pi4.bkt.clouddn.com/build-blog-two/github_new_repository.png)
<p id="div-border-left-green">- repository name 一定要填 Owner.github.io，例如：songguoping.github.io
- 项目一定是 Public
</p>


### 安装 Git 部署器
在终端执行以下命令；
```bash
$ npm install hexo-deployer-git –save
```
### 修改配置文件
打开 <span id="inline-blue">站点配置文件</span>，搜索 `deploy`，修改如下配置：
```bash
deploy:
  type: git
  repo: 
      github: https://github.com/songguoping/songguoping.github.io.git
  branch: master
```

### 部署
在站点目录下执行以下命令，即可将博客部署到 GitHub 上：
```bash
$ hexo generate
$ hexo deploy
```
部署成功以后，在浏览器中输入 `http://Repository name`，例如 [http://songguoping.github.io](http://songguoping.github.io) 即可在线浏览自己的博客啦

## 部署到 Coding
### 创建项目
没有 Coding 账号的先注册账号，然后新建项目，具体如图：
![coding_new_repository](http://p0ufe2pi4.bkt.clouddn.com/build-blog-two/coding_new_repository.png)
<p id="div-border-left-green">- 项目名称和个人名称必须一致
- 项目必须是公开的</p>

### 修改配置文件
打开 <span id="inline-blue">站点配置文件</span>，搜索 `deploy`，修改如下配置：
```bash
deploy:
  type: git
  repo: 
      github: https://github.com/songguoping/songguoping.github.io.git
      coding: https://git.coding.net/codersong/codersong.git
  branch: master
```
配置完成后，使用 `hexo d -g` 命令重新生成博客并部署到 GitHub 和 Coding 上去。

### 为博客绑定个人域名
#### 购买域名
你需要在[万网](www.net.cn)上购买一个域名，笔者购买的域名是：codersong.top

#### 解析域名
找一个域名解析的提供商，使用它的DNS解析服务，此处推荐 [DNSPod](https://www.dnspod.cn)
在your-hexo-site/source/路径下创建一个名为 `CNAME` 的文件`(没有扩展名)`,如下图所示：
![cname](http://p0ufe2pi4.bkt.clouddn.com/build-blog-two/cname.png)
使用文本编辑器打开 `CNAME` 文件，在首行添加个人域名信息，如：codersong.top
完成以上步骤后，重新执行命令 `hexo d -g` 部署。

### 通过 DNSPod 配置 DNS 流量分发
注册账号登陆 DNSPod，在自己的域名下添加四条记录：
<p id="div-border-top-blue">添加一个主机记录为 `@`，记录类型为 `CNAME`，线路类型为 `国内`，记录值为 `xxx.coding.me`(例如：songguoping.coding.me)的记录；</p>
<p id="div-border-top-blue">添加一个主机记录为 `www`，记录类型为 `CNAME`，线路类型为 `国内`，记录值为 `xxx.coding.me`(例如：songguoping.coding.me)的记录；</p>
<p id="div-border-top-blue">添加一个主机记录为 `@`，记录类型为 `CNAME`，线路类型为 `默认`，记录值为 `xxx.github.io`(例如：songguoping.github.io)的记录；</p>
<p id="div-border-top-blue">添加一个主机记录为 `@`，记录类型为 `CNAME`，线路类型为 `国内`，记录值为 `xxx.github.io`(例如：songguoping.github.io)的记录；</p>
如下图所示：
![dnspod](http://p0ufe2pi4.bkt.clouddn.com/build-blog-two/dnspod.png)
<p id="div-border-left-green">主机记录为 @ 允许用户以 codersong.top 访问你的博客；主机记录为 www 允许用户以 www.codersong.top 访问你的博客</p>


### 配置 Coding Pages
#### 自定义域名
如下图所示：
![coding_yuming](http://p0ufe2pi4.bkt.clouddn.com/build-blog-two/coding_yuming.png)
<p id="div-border-left-green">域名可以绑定最多5个，一定要选`首选`的域名，并且非首选域名要勾选`跳转至首选`域名不然有些第三方服务数据会统计不到一起</p>

#### 强制 https 访问
![coding_https](http://p0ufe2pi4.bkt.clouddn.com/build-blog-two/coding_https.png)
<p id="div-border-left-green">- 先要验证，验证完成后如图
- 打开强制 https 访问的开关，这时访问你的博客都是 https 了</p>

#### 屏蔽掉 Coding 跳转页
将博客部署 Coding 后，访问你的博客首先会跳到 Coding 的一个页面，这样不利于 seo 优化，我们要屏蔽掉这个跳转页，如下图所示：
![coding_page_service](http://p0ufe2pi4.bkt.clouddn.com/build-blog-two/coding_page_service.png)
将文字版的代码放置到 `themes/next/layout/_partials/footer.swig` 中，可以替换掉 Hexo 的内容：
```bash
{% if theme.footer.powered %}
  <div class="powered-by">Hosted by <a rel="external nofollow" href="https://pages.coding.me" style="font-weight: bold">Coding Pages</a></div>
{% endif %}
```
代码配置完成后，勾选上图的`已放置 Hosted by Coding Pages`，等待通过审核。

通过 DNSPod 完成流量分发后，然后 Github 和 Coding 上完成各自的配置就可以将自己的域名与 Github page 和 Coding page 绑定起来。

## 总结
本章节我们主要介绍了如何把博客部署在 GitHub page 和 Coding Page 上，注册了自己的域名，通过 DNSpod 配置DNS流量分发，[下一章节](/2017-12-13/build-blog-two.html)主要介绍 NexT 的主题配置、第三方服务以及动画的实现。

## 常见问题
### 部署失败
检查 SSH 公钥是否配置

## 参考
>lijiankun24: http://lijiankun24.com/




