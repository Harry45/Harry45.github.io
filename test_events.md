---
layout: page
title: Test Events
permalink: /test_events/
---

Tell us about your blog. Hopefully it's cool.

<ul class="listing">
{% for test_event in site.test_events %}
  {% capture y %}{{test_event.date | date:"%Y"}}{% endcapture %}
  {% if year != y %}
    {% assign year = y %}
    <li class="listing-seperator">{{ y }}</li>
  {% endif %}
  <li class="listing-item">
    <time datetime="{{ test_event.date | date:"%Y-%m-%d" }}">{{ test_event.date | date:"%Y-%m-%d" }}</time>
    <a href="{{ test_event.url }}" title="{{ test_event.title }}">{{ test_event.title }}</a>
  </li>
{% endfor %}
</ul>