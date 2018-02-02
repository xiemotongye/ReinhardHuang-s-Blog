---
layout: post
title: 初识 python 爬虫
date: 2017-09-07 23:47:54.000000000 +08:00
tags: python
articleId: 20170907
---

## 什么是网络爬虫
大数据时代，获得完整全面的数据，是件极其重要却并不容易的事情。要真正做好大数据时代的分析，就需要借助爬虫的力量。 

>网络爬虫（又被称为网络蜘蛛，网络机器人），是一种按照一定的规则，自动地抓取万维网信息的程序或者脚本。

如果把互联网比喻成一个蜘蛛网，那么 **网络爬虫** 就是在网上爬来爬去的蜘蛛。**网络爬虫** 通过网页的链接地址来寻找网页，从网站某一个页面（通常是首页）开始，读取网页的内容，找到在网页中的其它链接地址，然后通过这些链接地址寻找下一个网页，一直遍历下去，直到把整个网站所有的网页都抓取完为止。 

## 网络爬虫的合法性

### 网络爬虫引发的问题

* 性能骚扰
* 法律风险
* 隐私泄露

#### 网络爬虫的"性能骚扰"
web服务器默认接受人类访问，受限于编写水平和目的，网络爬虫将会为web服务器带来巨大的资源的开销。
#### 网络爬虫的法律风险
服务器上的数据有产权归属，网络爬虫获取数据后牟利将会带来法律的风险。
#### 网络爬虫的隐私泄露
网络爬虫可能具备突破简单访问的控制能力，获取被保护的数据，从而泄露个人隐私。

无论如何，当你抓取某个网站的数据时，请记住自己是该网站的访客，应当约束自己的抓取行为，否则他们可能会封禁你的IP，甚至采取更进一步的法律行动。这就要求下载请求的速度要限定在一个合理值之内，并且还需要设定一个专属的用户代理来标识自己。

## Robots协议
> Robots协议（也称为爬虫协议、机器人协议等）的全称是“网络爬虫排除标准”（Robots Exclusion Protocol），网站通过Robots协议告诉搜索引擎哪些页面可以抓取，哪些页面不能抓取。

根据协议，网站管理员可以在网站域名的根目录下放一个robots.txt 文本文件，里面可以指定不同的网络爬虫能访问的页面和禁止访问的页面，指定的页面由正则表达式表示。网络爬虫在采集这个网站之前，首先获取到这个文件，然后解析到其中的规则，然后根据规则来采集网站的数据。

注意，这个协议的存在更多的是需要网络爬虫去遵守，而起不到防止爬虫的功能。

### 为什么需要Robots协议
互联网上的网页是通过超级链接互相关联起来的，从而形成了网页的网状结构。爬虫的工作方式就像蜘蛛在网上沿着链接爬来爬去，最基本的流程可以简化如下：

1. 喂给爬虫一堆url，我们称之为种子(seeds)；
2. 爬虫抓取seeds，解析html网页，抽取其中的超级链接；
3. 爬虫接着抓取这些新发现的链接指向的网页。

步骤2和步骤3循环往复。

了解了上面的流程就能发现：对爬虫来说网站非常被动，只有老老实实被抓取的份。

所以，对于网站的管理者来说，就存在这样的需求：

某些路径下是个人隐私或者网站管理使用，不想被搜索引擎抓取，比如说日本爱情动作片；
不喜欢某个搜索引擎，不愿意被他抓取，最有名的就是之前淘宝不希望被百度抓取；
小网站使用的是公用的虚拟主机，流量有限或者需要付费，希望搜索引擎抓的温柔点；
某些网页是动态生成的，没有直接的链接指向，但是希望内容被搜索引擎抓取和索引。

网站内容的所有者是网站管理员，搜索引擎应该尊重所有者的意愿，为了满足以上等等，就需要提供一种网站和爬虫进行沟通的途径，给网站管理员表达自己意愿的机会。有需求就有供应，robots协议就此诞生。

### Robots写法

最简单的robots.txt只有两条规则：

* User-agent：指定对哪些爬虫生效
* Disallow：不允许爬取的目录

另外常见的附加规则：

* Crawl-delay: 指定爬虫抓取的间隔时间（秒）。这种操作可以进行缓解服务器压力。
* Sitemap: 网站地图，格式为XML。其中列出网站中的网址以及关于每个网址的其他数据(上次更新的时间、更改的频率以及相对于网站上其他网址的重要程度等等)，利用这些信息搜索引擎可以更加智能地抓取网站内容。
* Allow: 允许爬取的目录，一般和Disallow配合使用。

### 举个🌰

知乎的robots文件: [https://www.zhihu.com/robots.txt](https://www.zhihu.com/robots.txt)

```
User-agent: *
Crawl-delay: 10

Disallow: /login
Disallow: /logout
Disallow: /resetpassword
Disallow: /terms
Disallow: /search
Disallow: /notifications
Disallow: /settings
Disallow: /inbox
Disallow: /admin_inbox
Disallow: /*?guide*
Disallow: /people/*-*-*-*
```

简书的robots文件：[http://www.jianshu.com/robots.txt](http://www.jianshu.com/robots.txt)

```
# See http://www.robotstxt.org/wc/norobots.html for documentation on how to use the robots.txt file
#
# To ban all spiders from the entire site uncomment the next two lines:
User-agent: *
Disallow: /search
Disallow: /notes/
Disallow: /admin/
Disallow: /p/0826cf4692f9
Disallow: /p/d8b31d20a867
Disallow: /collections/*/recommended_authors
Disallow: /trial/*
Disallow: /mobile/campaign/fd2017/*
```

淘宝的robots文件：[https://www.taobao.com/robots.txt](https://www.taobao.com/robots.txt)

```
User-agent:  Baiduspider
Allow:  /article
Allow:  /oshtml
Allow:  /wenzhang
Disallow:  /product/
Disallow:  /

User-Agent:  Googlebot
Allow:  /article
Allow:  /oshtml
Allow:  /product
Allow:  /spu
Allow:  /dianpu
Allow:  /wenzhang
Allow:  /oversea
Disallow:  /

User-agent:  Bingbot
Allow:  /article
Allow:  /oshtml
Allow:  /product
Allow:  /spu
Allow:  /dianpu
Allow:  /wenzhang
Allow:  /oversea
Disallow:  /

User-Agent:  360Spider
Allow:  /article
Allow:  /oshtml
Allow:  /wenzhang
Disallow:  /

User-Agent:  Yisouspider
Allow:  /article
Allow:  /oshtml
Allow:  /wenzhang
Disallow:  /

User-Agent:  Sogouspider
Allow:  /article
Allow:  /oshtml
Allow:  /product
Allow:  /wenzhang
Disallow:  /

User-Agent:  Yahoo!  Slurp
Allow:  /product
Allow:  /spu
Allow:  /dianpu
Allow:  /wenzhang
Allow:  /oversea
Disallow:  /

User-Agent:  *
Disallow:  /
```

**Robots协议不是什么技术壁垒，而只是一种互相尊重的协议。Robots协议是建议但非约束性，网络爬虫可以不遵守，但存在法律风险。**

## 估算网站大小
目标网站的大小会影响我们如何进行爬取。如果是只有几百个URL的网站，效率并没有那么重要；但如果是拥有数百万个网页的站点，使用串行下载可能需要持续数月才能完成，这时就需要使用分布式下载来解决。

估算网站大小的一个简便方法是检查其他搜索引擎爬虫的结果。可以通过site参数获取该信息。如在 Google 中搜索 site:jianshu.com

![](http://upload-images.jianshu.io/upload_images/2422746-dab25c6c0e3544fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/2422746-145f15e734af7ab6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/2422746-8d4eca78217e87f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

三个搜索引擎显示简书的网页数量都在百万级。

在域名后面添加URL路径可以对搜索结果进行过滤。

## 识别网站所用技术
构建网站的技术类型也会对我们如何爬取产生影响。网站所用的框架，以及 AngularJS 之类的动态加载，ASP.NET 的会话管理和表单提交都会影响我们的爬虫策略。

有一个十分有用的工具可以检查网站构建的技术类型——builtwith 模块（只支持python2环境，python3环境报错）。

安装builtwith之前，需要安装pip。

```
sudo easy_install pip
```
>pip 是一个现代的，通用的 Python 包管理工具。提供了对 Python 包的查找、下载、安装、卸载的功能。

pip之于python相当于maven之于Java，CocoaPos之于OC。

pip安装完成后，安装builtwith模块。

```
pip install builtwith
```

该模块将 URL 作为参数，下载该 URL 并对其进行分析，然后返回该网站使用的技术。

举个🌰：

![](http://upload-images.jianshu.io/upload_images/2422746-6fafe75fd329ad1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 识别网站所有者

对于一些网站，我们可能会关心其所有者是谁。为了找到网站的所有者，我们可以使用WHOIS协议查询域名的注册者是谁。Python中有一个对该协议的封装库。我们可以通过pip进行安装。

```
pip install python-whois
```

举个🌰：

![](http://upload-images.jianshu.io/upload_images/2422746-2c2a0136994177c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 编写第一个python爬虫

从最简单的开始，使用最好用的 requests 做网络访问，并且使用操作方便的 beautifulsoup 来解析 html。Python 标准库中自带了 xml 模块，但是性能不够好，而且缺乏一些人性化的 API，相比之下，第三方库 lxml 增加了很多实用功能，性能也更高，可谓爬虫处理网页数据的一件利器。

```
pip install requests
pip install bs4
pip install lxml
```

我们练手的项目就是爬取简书新上榜推荐文章的数据。

```
#coding=utf-8
import requests
import time
from bs4 import BeautifulSoup

base_url = 'http://www.jianshu.com'
add_url = '/recommendations/notes'
num = 0
nowtime = time.time()

while(True):
    try:
        if num > 1000:
            break
        first_page = requests.request('get', base_url+ add_url).content
        soup = BeautifulSoup(first_page, "lxml")
        title_list = [i.get_text() for i in soup.select("a.title")]
        for i in title_list:
            num+=1
            print(num, '  ', i)
        try:
            nowtime = int(nowtime - 1800)
            add_url = '/recommendations/notes?category_id=56&max_id=' + str(nowtime)
        except:
            break
        time.sleep(10)
    except Exception as e:
        print(e)
        break
```

非常简单的代码，难点在于分页。

稍微研究了一下，打开浏览器的开发者工具，发现每次点击更多按钮，network 发送的请求都带了一个 *max_id* 参数。这里 *max_id* 显然是一个 timestamp，多点击几次后发现，这个 timestamp 基本上是以半小时为单位递减的。于是通过每次改变 *max_id* 我们实现了爬虫的自动分页功能。

这就是一个最简单的爬虫，一般真正的爬虫还需要涉及数据存储，这个就交给读者们自己解决吧。

## 参考

* 《用 Python 写网络爬虫》—— Richard Lawson 著，李斌 译
* [爬虫的"盗亦有道"-Robots协议](http://www.jianshu.com/p/d16076661d40)—— 若与
* [新手向爬虫（一）利用工具轻松爬取简书并分析](http://www.jianshu.com/p/be891e7e96e2) —— treelake