---
layout: page
title: Links
description: 没有链接的博客是孤独的
keywords: 友情链接
comments: true
menu: 链接
permalink: /links/
---

> 记录一些别人的博客地址

{% for link in site.data.links %}
* [{{ link.name }}]({{ link.url }})
{% endfor %}
