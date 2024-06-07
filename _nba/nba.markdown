---
layout: default
title: NBA
---

<h1 class="mt-4">NBA</h1>
{% assign nba = site.nba | sort: "year" | reverse %}
{% for pub in nba %}
<div class="pubitem">
  <div class="pubteaser">
    <a href="{{pub.url}}">
      <img src="/images/{{ pub.slug }}_small.png" alt="{{pub.slug}} publication teaser"/>
    </a>
  </div>
  <div class="pubtitle">
    {{ pub.title }}
  </div>
  <div class="publinks">
    <a href="/download/{{ pub.slug}}.pdf"><i class="far fa-file-pdf"></i> PDF</a>&nbsp;&nbsp;
    <a href="{{pub.url}}"><i class="fas fa-arrow-right"></i> Project Page</a>
  </div>
</div>
{% endfor %}