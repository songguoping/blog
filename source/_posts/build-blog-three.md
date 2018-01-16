---
title: 使用 hexo-theme-next 搭建博客（三）
date: 2017-12-15 
description: 本章节主要介绍 NexT的主题配置、第三方服务以及动画的实现
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

[上一篇文章](/2017-12-14/build-blog-two.html)我们主要介绍了如何把博客部署在 GitHub page 和 Coding Page 上，注册了自己的域名，通过 DNSpod 配置 DNS 流量分发，本章节主要介绍 NexT 的主题配置、第三方服务以及动画的实现。

## NexT 的主题配置 
具体的主题配置在 NexT 文档上有，我这里主要介绍没有讲到的地方以及一些坑。
### 代码高亮
在主题配置文件中配置如下：
```bash
# Code Highlight theme
# Available value: normal | night | night eighties | night blue | night bright
# https://github.com/chriskempson/tomorrow-theme
highlight_theme: normal
```
可能会发现插入代码后代码不高亮，此刻检查你的多行或者一行代码的 markdown 格式是否正确。代码凌乱的话，建议使用空格代替 tab，例：
```java
public class HelloWorld { 
    public static void main(String args[]) { 
        System.out.println("Hello World!"); 
    } 
}
```

### 修改文章内链接文本样式
修改文件 `themes\next\source\css\_common\components\post\post.styl` ，在末尾添加如下 css 样式：

```css
// 文章内链接文本样式
.post-body p a{
  color: #0593d3;
  border-bottom: none;
  border-bottom: 1px solid #0593d3;
  &:hover {
    color: #fc6423;
    border-bottom: none;
    border-bottom: 1px solid #fc6423;
  }
}
```
其中选择 .post-body 是为了不影响标题，选择 p 是为了不影响首页“阅读全文”的显示样式,颜色可以自己定义。

### 修改文章底部的那个带 # 号的标签
修改模板 `/themes/next/layout/_macro/post.swig`，搜索 `rel="tag">#`，将 # 换成 `<i class="fa fa-tag"></i>`
此处图标是 NexT 使用的是 [Font Awesome](http://fontawesome.io/icons/) 提供的图标， Font Awesome 提供了 600+ 的图标，可以满足绝大的多数的场景。

### 在每篇文章末尾统一添加“本文结束”标记
在路径 `\themes\next\layout\_macro` 中`新建 passage-end-tag.swig` 文件,并添加以下内容：
```html
<div>
    {% if not is_index %}
        <div style="text-align:center;color: #ccc;font-size:14px;">-------------本文结束<i class="fa fa-hourglass-end"></i>感谢您的阅读-------------</div>
    {% endif %}
</div>
```
接着打开 `\themes\next\layout\_macro\post.swig`文件，在 post-body 之后， post-footer 之前添加如下画红色部分代码:
```html
<div>
  {% if not is_index %}
    {% include 'passage-end-tag.swig' %}
  {% endif %}
</div>
```

然后打开<span id="inline-purple">主题配置文件</span>在末尾添加：
```bash
# 文章末尾添加“本文结束”标记
passage_end_tag:
  enabled: true
```

### 实现文章字数统计功能
在根目录下安装 `hexo-wordcount`,运行：
```bash
$ npm install hexo-wordcount --save
```
然后在<span id="inline-purple">主题配置文件</span>中，配置如下：
```bash
# Post wordcount display settings
# Dependencies: https://github.com/willin/hexo-wordcount
post_wordcount:
  item_text: true
  wordcount: true
  min2read: true
```

### 在文章底部增加版权信息
在<span id="inline-purple">主题配置文件</span>中配置如下：
```bash
# Declare license on posts
post_copyright:
  enable: true
  license: CC BY-NC-SA 3.0
  license_url: https://creativecommons.org/licenses/by-nc-sa/3.0/
```

### 修改打赏字体不闪动
修改文件 `next/source/css/_common/components/post/post-reward.styl`，然后注释其中的函数 `wechat:hover` 和 `alipay:hover`，如下：
```bash
/* 注释文字闪动函数
 #wechat:hover p{
    animation: roll 0.1s infinite linear;
    -webkit-animation: roll 0.1s infinite linear;
    -moz-animation: roll 0.1s infinite linear;
}
 #alipay:hover p{
   animation: roll 0.1s infinite linear;
    -webkit-animation: roll 0.1s infinite linear;
    -moz-animation: roll 0.1s infinite linear;
}
*/
```

### 自定义页面
比如想增加一个留言页面，则可以执行以下步骤：
1. 执行 "hexo new page "guestbook"
2. 找到主题配置文件下的 menu，把 guestbook 加进去：
```bash
menu:
 home: /
 categories: /categories
 about: /about
 archives: /archives
 tags: /tags
 guestbook: /guestbook

 menu_icons:
  enable: true
  guestbook: book # 使用 fontawesome
```
3. 在 `/themes/next/languages/zh-Hans.yml`的目录下（这里默认你使用的是简体中文，若是其他语言更改相应的 yml 就行），在 memu 下加一句即可：
```bash
guestbook: 留言
```

## 第三方服务
### HyperComments 评论系统
使用自己的 google 账号登录 [HyperComments](https://www.hypercomments.com/)，添加 blog 的 url,例：xxx.github.io，在 `setting -> Widget -> General` 中可以看到 ID，如下图所示：
![hypercomments_id](http://p0ufe2pi4.bkt.clouddn.com/build-blog-three/hypercomments_id.png)
### 不蒜子统计
编辑<span id="inline-purple">主题配置文件</span>中的 `busuanzi_count` 的配置项。
当 enable: true 时，代表开启全局开关。若 site_uv、site_pv、page_pv 的值均为 false 时，不蒜子仅作记录而不会在页面上显示。

#### 站点 UV 配置
当 site_uv: true 时，代表在页面底部显示站点的 UV 值。
site_uv_header 和 site_uv_footer 为自定义样式配置，相关的值留空时将不显示，可以使用（带特效的）font-awesome。显示效果为 [site_uv_header] UV 值 [site_uv_footer]。
```bash
site_uv: true
site_uv_header: 本站访客数
site_uv_footer: 人次
```

#### 站点 PV 配置
当site_pv: true 时，代表在页面底部显示站点的 PV 值。
site_pv_header 和 site_pv_footer 为自定义样式配置，相关的值留空时将不显示，可以使用（带特效的）font-awesome。显示效果为 [site_pv_header] PV 值 [site_pv_footer]。
```bash
site_pv: true
site_pv_header: 本站总访问量
site_pv_footer: 次
```

### 文章的阅读次数统计

### AddThis 内容分享
在网站 [AddThis](http://www.addthis.com/) 上注册账号。 可以使用 Google/Facebook/Twitter 账号进行第三方登陆
选择分享模块的显示方式是 `Inline`，调整分享模块的大小等参数
从下面菜单获得 AddThis id：`More.. --> General --> ID`。如下所示：
![addthis_share](http://p0ufe2pi4.bkt.clouddn.com/build-blog-three/addthis_share.png)
在 主题配置文件 中，把 #Share 下的 #add_this_id 取消注释， 改为 `add_this_id: put_your_add_this_id_here`
在 `/themes/next/layout/_macro/post.swig` 里面的 </header> 标签的上方添加如下代码：
```bash
< {##AddThis 分享,首页不显示##}
{% if theme.add_this_id and not is_index%}
{% include '../_partials/share/add-this.swig' %}
{% endif %}
```

## 动画
### 添加动态背景
1. 将 `particle.js` 文件添加到 `\themes\next\source\js\src` 文件目录下。
2. 找到 `\themes\next\layout\_layout.swing` 文件， 在文件的后面，</body>标签之前 添加以下代码：

```js
<!-- 背景动画 -->
<script type="text/javascript" src="/js/src/particle.js"></script>
```

### 修改作者头像并旋转
打开 \themes\next\source\css\ _common\components\sidebar\sidebar-author.styl，在里面添加如下代码：
```bash
.site-author-image {
  display: block;
  margin: 0 auto;
  padding: $site-author-image-padding;
  max-width: $site-author-image-width;
  height: $site-author-image-height;
  border: $site-author-image-border-width solid $site-author-image-border-color;

  /* 头像圆形 */
  border-radius: 80px;
  -webkit-border-radius: 80px;
  -moz-border-radius: 80px;
  box-shadow: inset 0 -1px 0 #333sf;

  /* 设置循环动画 [animation: (play)动画名称 (2s)动画播放时长单位秒或微秒 (ase-out)动画播放的速度曲线为以低速结束 
    (1s)等待1秒然后开始动画 (1)动画播放次数(infinite为循环播放) ]*/
 

  /* 鼠标经过头像旋转360度 */
  -webkit-transition: -webkit-transform 1.0s ease-out;
  -moz-transition: -moz-transform 1.0s ease-out;
  transition: transform 1.0s ease-out;
}

img:hover {
  /* 鼠标经过停止头像旋转 
  -webkit-animation-play-state:paused;
  animation-play-state:paused;*/

  /* 鼠标经过头像旋转360度 */
  -webkit-transform: rotateZ(360deg);
  -moz-transform: rotateZ(360deg);
  transform: rotateZ(360deg);
}

/* Z 轴旋转动画 */
@-webkit-keyframes play {
  0% {
    -webkit-transform: rotateZ(0deg);
  }
  100% {
    -webkit-transform: rotateZ(-360deg);
  }
}
@-moz-keyframes play {
  0% {
    -moz-transform: rotateZ(0deg);
  }
  100% {
    -moz-transform: rotateZ(-360deg);
  }
}
@keyframes play {
  0% {
    transform: rotateZ(0deg);
  }
  100% {
    transform: rotateZ(-360deg);
  }
}

```

### 实现点击出现桃心效果
在网址输入如下
```bash
http://7u2ss1.com1.z0.glb.clouddn.com/love.js
```
然后将里面的代码 copy 一下，新建 `love.js` 文件并且将代码复制进去，然后保存。将 love.js 文件放到路径 `/themes/next/source/js/src` 里面，然后打开 `\themes\next\layout\_layout.swig` 文件,在末尾（在前面引用会出现找不到的 bug）添加以下代码：

```js
<!-- 页面点击小红心 -->
<script type="text/javascript" src="/js/src/love.js"></script>
```

## 总结
本章节主要介绍了 NexT 的主题配置、第三方服务以及动画的实现，更多的个人定制可以参考下面的网址，本系列到这里相信你已经可以定制自己风格的博客网站了，恭喜你！但做出自己喜欢的博客网站还只是第一步，我们还要将幸苦做出的博客网站推销出去，让更多的人可以看到，可以搜索到，所以[下一章节](/2017-12-16/build-blog-four.html)我们主要介绍网站的搜索优化以及数据统计分析。

## 参考
>https://segmentfault.com/a/1190000009544924



