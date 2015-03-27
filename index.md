---
layout: page
title: Hello Bacteria!
tagline: Supporting tagline
---
{% include JB/setup %}

# Mapping reads to genome. 

<img href="" src="{{ base.url }}/assets/images/Reads-mapping-to-Ra&Rv-ep.png" alt="RNA-Seq Mapping" border="0" width="700"/>    

<small>RNA-Seq analysis for bacteria.</small>


=====================================

Latest posts:   

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


