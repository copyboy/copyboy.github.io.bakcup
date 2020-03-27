---
layout: page
title: About
description: Better Me.
keywords: copyboy, 冬青
comments: true
menu: 关于
permalink: /about/
---

我是青冬，一枚后端开发。

对万物保持敬畏之心。

以及感谢成长过程中遇到的每个人。

## 联系

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
