---
layout: page
title: NBA Projects
---

<!-- {% for project in site.nba %}
  <div class="project">
    <h4>{{project.title}}</h4>
    {{project.content}}
  </div>
{% endfor %} -->

{% for project in site.nba %}
  <div class="project">
    <h4><a href="{{project.url}}">{{project.title}}</a></h4>
  </div>
{% endfor %}