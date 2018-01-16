---
title: 使用 hexo-theme-next 搭建博客（五）
date: 2018-1-14 
description: 本章节主要介绍 hexo 备份在 github 上、博客生成发表的流程、同步更新到新电脑
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
- 备份博客在 Github 上；
- 同步更新博客；
- ......
</p>

[上一篇文章](/2017-12-16/build-blog-four.html)我们主要介绍网站的搜索优化以及数据统计分析，本章节主要介绍将博客项目上传至 Github 备份、博客生成发表流程以及同步更新到新电脑。

## Github 备份
1. Fork [hexo-theme-next](https://github.com/iissnan/hexo-theme-next) 项目到自己的仓库
2. 将本地修改的主题文件夹 themes/next 备份到其它地方并删除
3. 运行以下命令将 Fork 出来的仓库 pull 到本地子模块
```bash
git submodule add https://github.com/YourName/hexo-theme-next.git themes/next
git checkout master
```
    运行该命令后会在项目根目录生成 `.gitmodules` 文件，文件内容如下：
    ```bash
    [submodule "themes/next"]
        path = themes/next
        url = https://github.com/songguoping/hexo-theme-next
    ```
4. 将备份的主题内容覆盖到刚拉下来的主题中，我们先要 push 子模块，在 theme/next 目录下依次执行：
```bash
git add .
git commit -m "update config file"
git push origin master //这是提交到fork后主题的仓库
```
5. 在 Github 上新建一个仓库 blog
6. 切换到项目根目录，在根目录下执行以下命令提交到 Github 上的 blog 仓库下：
```bash
git add .
git commit -m "add submodule"
git push origin master 
```

## 博客生成发表流程
如果改变了 `themes/next` 的内容则按以下流程：
1. 先在本地的 themes/next 下 git commit
2. 使用命令  git push origin master 提交到 Github(即 Fork 的那个仓库)
3. 在本地 hexo 下 git commit 
4. 使用命令提交到 Github
5. 以上成功后部署 blog


如果没有改变 themes/next 的内容则1，2步不用执行，原则就是`在提交项目到远程仓库时先提交该项目的子模块到远程仓库`。

## 同步更新到新电脑
配置好 Node 、git 环境，安装 Hexo 后：
```bash
git clone --recursive git@github.com:songguoping/blog.git
git submodule init
git submodule update
```
第一次初始化之后，子模块的 head 可能是游离的，如果直接提交了变更就会带来问题，记得先放到 master 上，运行:
```bash
cd themes/next
git checkout master
```
代码配置完成之后，执行:
```bash
npm install
```
就可以完成 package.json 中所有依赖的安装了，执行命令 hexo -s 在本地运行查看效果。

### 总结
本章节主要介绍将博客项目上传至 Github 备份、博客生成发表流程以及同步更新到新电脑。至此，本系列 `使用 hexo-theme-next 搭建博客`就全部更新完成了。该系列作为我开始写博客的练手之作，存在很多的不足，后期也会不定时的更新，希望读者多多批评指正！






