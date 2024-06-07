---
layout: page
title: NBA Projects
---

{% for cookie in site.nba %}
  <div class="cookie">
    <!-- <h2><img src="{{ cookie.image_path }}" alt="{{ cookie.title }}">{{ cookie.title }}</a></h2> -->
    <h4>{{cookie.title}}</h4>
    {{ cookie.content }}
  </div>
{% endfor %}