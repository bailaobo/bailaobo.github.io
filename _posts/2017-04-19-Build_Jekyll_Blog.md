---
layout: post
title: 搭建Jekyll博客
author: BaiLaobo 
date: 2017-04-18 16:16:00
tags:
  - blog
categories: 
  - Tools
---

* content
{:toc}

>　　最近突然想写Blog，以前也反反复复的搭建了很多次站点，使用过WrodPress、Jekyll、Hexo。
选择越多就越难选择，想找个简洁的，又觉得功能又不够好。比如Hexo下的
[NexT](http://theme-next.iissnan.com/) 和 [cafe](https://github.com/giscafer/hexo-theme-cafe/) 主题，
Next风格简约可惜分类不能分级，cafe到是很不错,功能都有界面也不错。
但又不甘心Hexo的推送静态页面，所以放弃了Hexo系。
选择的Jekyll，Jekyll的好多主题都很简洁简洁到放一张大图，感觉太艺术不实用。
最终找到一个
不错的主题 [Cool Jekyll Blog Theme](https://github.com/Gaohaoyang/gaohaoyang.github.io) By [Gaohaoyang](https://github.com/Gaohaoyang) 




## 搭建过程
1.到 Gaohaoyang 的github主页上去 [fork](https://github.com/Gaohaoyang/gaohaoyang.github.io)工程 , 
把库名 **gaohaoyang**.github.io (粗体部分) 改成自己的的github用户名,不过这样还不能访问，还需要将_config.yml的选项改成自己的,不需要的注掉即可。

```
title: # 
brief-intro: # 
url: "https://xxxxx.github.io" #把 xxx 修改成自己的github用户名 网站才能访问
twitter_username: #
facebook_username: #
github_username: #
email: # 
weibo_username: # 
zhihu_username: # 
linkedIn_username: # 
description_footer: #
disqus_shortname: #  
baidu_tongji_id: #  
google_analytics_id: #
```
2. 修改index.html 的Welcome
3. 删除原作者的 posts 和 drafts 
4. 去掉Collections、Demo导航（删除page目录下 3collections.md、3demo.html 文档）
5. 修改关于我（page/4about.md）
6. md 文件的日期 不能是当日 否则本地Jekyll不显示这条记录  
