---
layout: page
title: Teaching
permalink: /teaching/
---

<section>
<h1>A-Level Mathematics (Syllabus Code - 9709)</h1>

<p align="justify">I'll briefly explain the A-Level Mathematics syllabus, as described by the CIE. Students should take the following two Pure Mathematics papers: </p>

</section>


<ul class="listing">
{% for post in site.teaching %}
  {% capture y %}{{post.date | date:"%Y"}}{% endcapture %}
  {% if year != y %}
    {% assign year = y %}
    <li class="listing-seperator">{{ y }}</li>
  {% endif %}
  <li class="listing-item">
    <time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time>
    <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
  </li>
{% endfor %}
</ul>


