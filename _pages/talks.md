---
layout: page
title: talks
permalink: /talks/
description: Some of my talks
nav: true
---

<div class="talks grid">

  {% assign sorted_talks = site.talks | sort: "importance" %}
  {% for talk in sorted_talks %}
  <div class="grid-item">
    {% if talk.redirect %}
    <a href="{{ talk.redirect }}" target="_blank">
    {% else %}
    <a href="{{ talk.url | relative_url }}">
    {% endif %}
      <div class="card hoverable">
        {% if talk.img %}
        <img src="{{ talk.img | relative_url }}" alt="project thumbnail">
        {% endif %}
        <div class="card-body">
          <h2 class="card-title ">{{ talk.title }}</h2>
          <p class="card-text">{{ talk.description }}</p>
    </a>
          <p class="card-text">{{ talk.content }}</p>
          <div class="row ml-1 mr-1 p-0">
          </div>
        </div>
      </div>

  </div>
{% endfor %}

</div>