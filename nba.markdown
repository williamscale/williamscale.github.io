---
layout: page
title: NBA Projects
---

{% for cookie in site.nba %}
  <div class="cookie">
    <h4>{{cookie.title}}</h4>
    {{ cookie.content }}
  </div>
{% endfor %}