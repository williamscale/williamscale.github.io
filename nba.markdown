---
layout: page
title: NBA
---

{% for cookie in site.nba %}
  <div class="cookie">
    <h2><img src="{{ cookie.image_path }}" alt="{{ cookie.title }}">{{ cookie.title }}</a></h2>
    {{ cookie.content }}
  </div>
{% endfor %}