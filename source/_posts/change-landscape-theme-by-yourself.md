title: 教你定制Hexo的landscape打造自己的主题
toc: true
tags: Hexo
categories: 实用技能
date: 2016-03-14 16:26:13
---

我敢肯定大家不仅想拥有自己的博客，还想有一套自己的主题。目前Hexo的主题多数都是从官方主题landscape修改而成的，然而别人能修改，你也可以修改，就算你不会编程，不会web前端，跟着我做你一样可以做出自己想要的主题样式，打造属于你自己的主题。ps:我也是一点一点摸索的，参考了很多教程。

<!--more-->

# 创建博客
关于怎么安装Hexo和创建博客请看我博客的另一篇文章  
http://www.codertian.com/2015/11/26/github-hexo-blog/

那么假设大家都已经安装好了博客，因为Hexo的默认主题就是``landscape``所以大家不需要更换其他主题

**以下配置，请大家先``hexo clean``一下，再发布**

# landscape的配置文件
```
# Header
menu:
  Home: /
  Archives: /archives
rss: /atom.xml

# Content
excerpt_link: Read More
fancybox: true

# Sidebar
sidebar: right //插件可以放左边或右边
widgets:
- category
- tag
- tagcloud
- archive
- recent_posts
```

* 更改左上角菜单请更改header区域，格式复制一下自己改
* 如果想改变more分割线显示的提示，可以更改Read More
* 删除插件可以在widgets中，关于添加一个再讲

# 更改banner图片
图片的位置为：``landscape/source/css/images``目录下面，可以替换为你自己想要的图片。
更改banner栏的大小就去``landscape/source/css/_variables.styl``找到下面一段修改一下

```
// Header
logo-size = 40px
subtitle-size = 16px
banner-height = 200px //可以更改为自己喜欢的banner高度
banner-url = "images/banner.jpg" //图片名称也可以修改
```

# 更改侧边栏连接的颜色

还是路径``landscape/source/css/_variables.styl``找到

```
// Colors
color-default = #555 
color-grey = #ec4c02
color-border = #ddd //更改边框的颜色
color-link = #0072a3 //更改连接的颜色
color-background = #eee //页面的背景颜色 
color-sidebar-text = #777 //貌似当时修改的这个吧
color-widget-background = #ddd //边栏插件的背景颜色
color-widget-border = #ccc //边栏插件的边框颜色
color-footer-background = #262a30 //页面底部的背景颜色
color-mobile-nav-background = #191919
color-twitter = #00aced
color-facebook = #3b5998
color-pinterest = #cb2027
color-google = #dd4b39

```

这些颜色都是CSS颜色。这时候可能有小伙伴有疑问“我怎么知道我想要的颜色是什么？”``Mac``上大家可以去``AppStore``下载``sip``这个是一个免费的取色软件，比``Mac``自带的好用的多。``Windows``上就有个更牛逼的软件了叫``FastStone Capture``，啥功能都有，大家自己去下载。

# 更改显示字体

还是同样一个文件，找到fonts

```
// Fonts
font-sans = "Helvetica Neue", Helvetica, Arial, sans-serif
font-serif = Georgia, "Times New Roman", serif
font-mono = Menlo, "Source Code Pro", Consolas, Monaco, Consolas, monospace 
```

这里面也可以更改字体的大小与间距，包括代码显示的间距

# 更改页面布局

还是同一个文件中，找到layout，可以更改整个页面的布局，更改页面的宽度，间距等，建议大家也不要乱改，不然可能会电脑上看着还是好的，手机上看着就不行了。

```
// Layout
block-margin = 20px //更改模块之间的间距
article-padding = 20px
mobile-nav-width = 280px
main-column = 11 //更改文章的宽度
sidebar-column = 3
```

# 更改文章背景

找到``landscape/source/css/_extend.styl``

```
$block
  background: #fbfbfb //文章的背景颜色
  /*box-shadow: 1px 2px 3px #ddd*/
  border: 1px solid color-border //文章的边框
  border-radius: 10px //设置文章页面圆角
```

# 更改代码样式

找到``landscape/source/css/_partial/highlight.styl``

```
$code-block
  background: highlight-background
  border-radius: 5px // 更改为圆角


$line-numbers
  color: #666
  font-size: 0.85em // 更改行号大小
```

小代码块的更改

```
.article-entry
  pre, code
    font-family: font-mono
  code
    background: #e3e3e3 设置背景颜色
    color: #666
    border-radius: 3px // 圆角设置
    padding: 0.1em 0.3em // 控制大小

```

小代码块的颜色更改

找到``landscape/source/css/_partial/article.styl``文件

```
.article-entry
  @extend $base-style
  clearfix()
  color: color-default
  padding: 0 article-padding
  p, table
    line-height: line-height
    margin: line-height 0
  h1, h2, h3, h4, h5, h6
    font-weight: bold
  h1, h2, h3, h4, h5, h6
    line-height: line-height-title
    margin: line-height-title 0
  code
    color: color-grey //设定文章小代码块字体颜色
```

# 给侧边栏插件添加外链

首先，在``/layout/_widget/``目录下新建一个文件，复制个当前目录的``archive.ejs``内容，我就命名为``linkss.ejs``

```
<div class="widget-wrap">
    <h3 class="widget-title"><%= __('链接') %></h3>
    <div class="widget">
      <li><a href="https://github.com/CoderTian" title="Zippera's Blog">我的github</a></li>
    </div>
  </div>

```

然后修改主题的``_config.yml``文件

```
# Sidebar
sidebar: right
widgets:
- category
- tag
- tagcloud
- archive
- recent_posts
- links //新添加的那个外链
```

# 添加多说
首先去多说官网，注册一个账号，点击我要安装，一步一步配置自己的网站，然后找到``landscape/layout/_partial/article.ejs``添加下列代码

代码获取网址  
http://dev.duoshuo.com/threads/541d3b2b40b5abcd2e4df0e9

```
<% if (!index && post.comments){ %>
<section id="comments">

      <!-- 多说评论框 start -->
    <div class="ds-thread" data-thread-key="<%= post.layout %>-<%= post.slug %>" data-title="<%= post.title %>" data-url="<%= page.permalink %>"></div>
    <!-- 多说评论框 end -->
    <!-- 多说公共JS代码 start (一个网页只需插入一次) -->
    <script type="text/javascript">
    var duoshuoQuery = {short_name:'<%= config.duoshuo_shortname %>'};
      (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0] 
         || document.getElementsByTagName('body')[0]).appendChild(ds);
      })();
      </script>
    <!-- 多说公共JS代码 end -->
  
</section>
```

然后在Hexo的配置文件``_config.yml``，注意是Hexo的
添加

```
duoshuo_shortname: moexcept
```

关于这个``duoshuo_shortname``就是你配置网站是的那个多说短域名
``moexcept.duoshuo.com``前面那个

# 添加百度分享

同样去百度搜索``百度分享``然后获取代码，在与上一步配置多说同一个文件中找到
粘贴到下面这句话下面，最好不要复制我的，大家可以自己去获取代码然后配置自己喜欢的样式

```
      <footer class="article-footer">
      
      <!--百度分享-->
      <div class="bdsharebuttonbox"><a href="#" class="bds_more" data-cmd="more"></a><a href="#" class="bds_qzone" data-cmd="qzone" title="分享到QQ空间"></a><a href="#" class="bds_tsina" data-cmd="tsina" title="分享到新浪微博"></a><a href="#" class="bds_tqq" data-cmd="tqq" title="分享到腾讯微博"></a><a href="#" class="bds_renren" data-cmd="renren" title="分享到人人网"></a><a href="#" class="bds_weixin" data-cmd="weixin" title="分享到微信"></a></div>
<script>window._bd_share_config={"common":{"bdSnsKey":{},"bdText":"","bdMini":"2","bdMiniList":false,"bdPic":"","bdStyle":"0","bdSize":"16"},"share":{},"image":{"viewList":["qzone","tsina","tqq","renren","weixin"],"viewText":"分享到：","viewSize":"16"}};with(document)0[(getElementsByTagName('head')[0]||body).appendChild(createElement('script')).src='http://bdimg.share.baidu.com/static/api/js/share.js?v=89860593.js?cdnversion='+~(-new Date()/36e5)];</script>
    
    </footer>
```

我貌似把原来的分享和标签给删除了，不然不好整样式。

# 添加文章目录

找到``layout/_partial/article.ejs``文件中添加

```
<% if (theme.excerpt_link){ %>
      <p class="article-more-link">
        <a href="<%- url_for(post.path) %>#more"><%= theme.excerpt_link %></a>
      </p>
    <% } %>
  <% } else { %>
  
    <!-- 文章目录 -->
    <% if (!index && post.toc){ %>
    <div id="toc" class="toc-article">
        <strong class="toc-title">文章目录</strong>
        <%- toc(post.content, {list_number: false}) %>
    </div>
    <% } %>

```

其中``list_number:false``表示不显示序号，如果你想要打开可以设置为``true``



# 更改归档显示的文章数

安装landscape可能你会发现归档页面显示的文章很少，就会又有新的一页，那么怎么更改呢？因为``landscape``主题是Hexo的默认主题，所以他和Hexo的配合度最高，可以看到Hexo的配置文件中

```
# Pagination
## Set per_page to 0 to disable pagination
per_page: 8
pagination_dir: page
```

这里控制了``主页``，``归档``，``分类``，``标签``页显示的文章数，所以这个数字对所有都生效，但是Hexo提供了插件对这几个进行分别控制

**设置archive页面数**

```
$ npm install hexo-generator-archive --save
```

``_config.yml``添加如下配置

```
archive_generator:
  per_page: 20  //为0时表示不分页全展示
  yearly: true  //按年生成归档
  monthly: true //按月生成归档
```

**设置tag页**

```
npm install hexo-generator-tag --save
```

``_config.yml``添加如下配置

```
tag_generator:
  per_page: 10
```

**设置category页**

```
npm install hexo-generator-category --save
```

```
category_generator:
  per_page: 10
```

# 添加RSS订阅

执行
```
npm install hexo-generator-feed --save
```

然后在hexo的``_config.yml``文件中添加

```
feed:
  type: atom //订阅烈性
  path: atom.xml //订阅路径
  limit: 20 //最大文章数
  hub:
```

然后重新提交网站。

# 添加sitemap
sitemap可以是搜索引擎抓取你的网页  
执行

```
npm install hexo-generator-sitemap --save
```

然后在hexo的``_config.yml``文件中添加

```
sitemap:
    path: sitemap.xml
```

好了本教程也就到这里了，里面内容可能有些疏漏，因为总结太晚，时间间隔太久了。本篇文章也大量参考了网上别人的文章，做了个大总结。
希望大家能多多支持我的博文，感谢大家。

我的博客地址http://www.codertian.com


