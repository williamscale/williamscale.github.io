---
layout: page
title: Projects
---

<!-- {% for project in site.nba %}
  <div class="project">
    <h4>{{project.title}}</h4>
    {{project.content}}
  </div>
{% endfor %} -->

{% for project in site.projects %}
  <div class="project">
    <h4><a href="{{project.url}}">{{project.title}}</a></h4>{{project.subtitle}}
    <!-- <h5><a href="{{project.url}}">{{project.subtitle}}</a></h6> -->
  </div>
{% endfor %}