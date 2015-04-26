---
layout: page
title: Hello Bacteria!
tagline: Supporting tagline
section: home
---
{% include JB/setup %}

# Mapping reads to genome. 

![](/assets/images/Reads-mapping-to-Ra&Rv-ep.png)

Figure. RNA-Seq analysis for bacteria.   

=====================================

# Latest posts:   

<ul class="posts">
  {% for post in site.posts limit:8 %}
  <li><span>{{ post.date | date: "%Y-%m-%d" }}</span> - <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>