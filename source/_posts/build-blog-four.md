---
title: 使用 hexo-theme-next 搭建博客（四）
date: 2017-12-16
description: 本章节主要介绍网站的搜索优化以及数据统计分析
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

[上一篇文章](/2017-12-15/build-blog-three.html)我们主要介绍 NexT 的主题配置、第三方服务以及动画的实现，本章节主要介绍网站的搜索优化以及数据统计分析。

## 网站的搜索优化
### 首页 title 的优化
文件路径是 `your-hexo-site\themes\next\layout` 下的 `index.swig` 文件，将下面代码
```bash
{% block title %}{{ config.title }}{% if theme.index_with_subtitle and config.subtitle %} - {{config.subtitle }}{% endif %}{% endblock %}
```
改成
```bash
{% block title %} {{ config.title }} - {{ theme.description }} {% endblock %}
```
这时候你的首页标题会更符合`网站名称 - 网站描述`习惯，为了做 seo 优化，把关键词也显示在 title 里，并尽可能的放在最前面，如下：
```bash
{% block title %} {{ theme.keywords }} - {{ config.title }} - {{ theme.description }
```
注意：别堆砌关键字，整个标题一般不超过80个字符，可以通过 [chinaz 站长工具](http://tool.chinaz.com/)的 seo 综合查询检查。

### 修改 permalink
hexo 默认的 permalink 配置是：
```bash
permalink: :year/:month/:day/:title/
```
不过这样可能对于搜索引擎不是太友好，我们可以把 permalink 修改成 .html 结尾和相应减少层级：
```bash
permalink: :year-:month-:day/:title.html
```
还有一点需要注意的是，title 最好是英文的，这样 permalink 也是不带中文的，但是 markdown 文件中的 title 你还是可以按自己的文章标题写成中文的。

### 给非友情链接的出站链接添加 “nofollow” 标签
经过 [chinaz 站长工具](http://tool.chinaz.com/)友情链接检测，发现有不必要的 PR 值输出，对于非友情链接的 PR 值输出，我们可以加上 nofollow 便签避免不必要的 PR 输出。
以 hexo 的 NexT 主题为例，需要修改两处
文件路径在 `your-hexo-site\themes\next\layout\_partials` 下的 `footer.swig`，将下面代码
```bash
{{ __('footer.powered', '<a class="theme-link" href="http://hexo.io">Hexo</a>') }}

<a class="theme-link" href="https://github.com/iissnan/hexo-theme-next">
```
在a标签中添加 `rel="external nofollow"` 改成
```bash
{{ __('footer.powered', '<a class="theme-link" href="http://hexo.io" rel="external nofollow">Hexo</a>') }}

<a class="theme-link" href="https://github.com/iissnan/hexo-theme-next" rel="external nofollow">
```
文件路径在 `your-hexo-site\themes\next\layout\_macro` 的 `sidebar.swig` 文件，将下面代码
```bash
<a href="{{ link }}" target="_blank">{{ name }}</a>

<a href="http://creativecommons.org/licenses/{{ theme.creative_commons }}/4.0" class="cc-opacity" target="_blank">
```
在a标签中添加 `rel="external nofollow"` 改成
```bash
<a href="{{ link }}" target="_blank" rel="external nofollow">{{ name }}</a>

<a href="http://creativecommons.org/licenses/{{ theme.creative_commons }}/4.0" class="cc-opacity" target="_blank" rel="external nofollow">
```

### 添加蜘蛛协议 robots.txt
在路径 `your-hexo-site\source\` 下新建文件 `robots.txt`，内容可参考如下：
```bash
# hexo robots.txt
User-agent: *
Allow: /
Allow: /archives/ 
Allow: /tags/ 
Allow: /categories/ 
Allow: /about/ 

Disallow: /js/
Disallow: /css/
Disallow: /fonts/
Disallow: /vendors/
Disallow: /fancybox/

Sitemap: http://codersong.top/sitemap.xml
Sitemap: http://codersong.top/baidusitemap.xml
```
注意，将 codersong.top 替换成你自己的域名，以上两个 sitemap 稍后会讲到

### 将博客添加 sitemap 站点地图
#### 安装 sitemap 站点地图自动生成插件
```bash
# google
npm install hexo-generator-sitemap --save
# baidu
npm install hexo-generator-baidu-sitemap --save
```
在<span id="inline-purple">主题配置文件</span>中添加以下配置：
```bash
sitemap: 
    path: sitemap.xml
baidusitemap:
    path: baidusitemap.xml
```
注意**缩进**，要不然生成的时候会报错
然后在<span id="inline-blue">站点配置文件</span>中修改 url 为你的域名，例如我的
```bash
url: http://codersong.top
```
以上配置好之后执行 `hexo g` 就能在 `your-hexo-site\public` 中生成 `sitemap.xml` 和 `baidusitemap.xml` 了。其中第一个是一会要提交给 google 的，后面那个看名字当然就是提交给 Baidu 的了。

#### 在 Google Baidu 验证 Hexo 博客网站
在提交 xml 文件前，Google Baidu 需要验证你提交的网站是不是你的，Google Baidu 上的验证方式差不多，此处只讲 Google 的验证方式。
登录 [Google Search Console](https://www.google.com/webmasters/tools/home)，点击添加属性，然后输入 Hexo 博客的网址，添加网站,如下图所示：
![google_add_site](http://p0ufe2pi4.bkt.clouddn.com/build-blog-four/google_add_site.png)
添加网站完成以后，需要验证网站。Google 提供了多种方案验证网站。这里使用 Google 推荐的一种 `域名提供商`方法，相对简单，如下图所示：
![google_site_check](http://p0ufe2pi4.bkt.clouddn.com/build-blog-four/google_site_check.png)
选择`其他`后如下图所示：
![google_site_checkout1](http://p0ufe2pi4.bkt.clouddn.com/build-blog-four/google_site_checkout1.png)
点击红框中的`添加 CNAME 记录`后如下图所示：
![google_site_checkout2](http://p0ufe2pi4.bkt.clouddn.com/build-blog-four/google_site_check2.png)
复制红框中的内容，打开 [DNSPod](https://www.dnspod.cn)，添加如下记录并保存:
![google_site_checkout_3](http://p0ufe2pi4.bkt.clouddn.com/build-blog-four/google_site_checkout_3.png)
回到刚才的页面点击验证，则验证通过。
#### 提交站点地图到 Google
验证完成后，就可以提交刚生成的 xml 站点地图了。注意，我们要先把博客部署到线上去，访问 [https://www.codersong.top/sitemap.xml](https://www.codersong.top/sitemap.xml)，如下图所示：
![google_sitemap](http://p0ufe2pi4.bkt.clouddn.com/build-blog-four/google_sitemap.png)
可以访问到 sitemap.xml 文件就将该文件提交到 Google，如下图所示：
![google_site_checkout4](http://p0ufe2pi4.bkt.clouddn.com/build-blog-four/google_site_checkout4.png)
按照以上步骤完成之后，自己的 Hexo 博客就提交到 Google 了，就可以通过 Google 搜索到你博客中相关的内容了。

#### 提交站点地图到 Baidu
登录[百度站长平台](http://zhanzhang.baidu.com/)，点击 `站点管理 ---> 添加站点`。流程走到验证网站的时候选择 `CNAME 验证`，方式同 Google 一样。
百度站长平台提供链接提交通道，可以提交想被百度收录的链接，百度搜索引擎会按照标准处理，但不保证一定能够收录您提交的链接,如下 baidusitemap 的提交方式。
![baidu_site_checkout](http://p0ufe2pi4.bkt.clouddn.com/build-blog-four/baidu_site_checkout.png)
还有我们可以再添加一种`自动推送`的方式，把如下的代码放入 `your-hexo-site/blog/themes/next/layout/_layout.swig` 的 <body> 标签的最后面即可：

```js
<script>
(function(){
    var bp = document.createElement('script');
    var curProtocol = window.location.protocol.split(':')[0];
    if (curProtocol === 'https') {
        bp.src = 'https://zz.bdstatic.com/linksubmit/push.js';
    }
    else {
        bp.src = 'http://push.zhanzhang.baidu.com/push.js';
    }
    var s = document.getElementsByTagName("script")[0];
    s.parentNode.insertBefore(bp, s);
})();
</script>
```
至此，理论上我们的博客就被 Google 和 Baidu 收录了，我们发现，现在在 Google 中搜索我们的网站可以搜索到，但是在 Baidu 中却搜索不到，这是因为 Baidu 现在还没收录我们的网站，可能要过一段时间。

## 博客数据统计分析
此处使用 [Google 分析](http://www.google.cn/intl/zh-CN_ALL/analytics/learn/index.html)，在填入媒体资源名称以及默认网址之后，在下图获取到跟踪 ID:
![google_analytics](http://p0ufe2pi4.bkt.clouddn.com/build-blog-four/google_analytics.png)
之后在主题配置文件中，修改字段 `google_analytics`， 值设置成你的 Google 跟踪 ID。

### 总结
本章节主要介绍网站的搜索优化以及数据统计分析，[下一章节]()主要介绍 hexo 博客生成发表的流程、markdown 写作的模版、






