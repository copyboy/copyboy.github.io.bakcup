---
layout: page
title: About
description: Better Me.
keywords: copyboy, 冬青
comments: true
menu: 关于
permalink: /about/
---



## 关于我

我是青冬，也可以叫我冬青。

野生开发程序员，对安全抱有兴趣。

对万物保持敬畏。

这里记录我学习思考的过程，

以及感谢成长过程中遇到的每个人。



## 点技能

![技能树]({{ 'http://img.zhangqingdong.cn/image-20200708153502398.png' }})



## 联系我

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
