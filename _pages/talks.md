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
          <h2 class="card-title text-lowercase">{{ project.title }}</h2>
          <p class="card-text">{{ project.description }}</p>
          <div class="row ml-1 mr-1 p-0">
            {% if talk.github %}
            <div class="github-icon">
              <div class="icon" data-toggle="tooltip" title="Code Repository">
                <a href="{{ project.github }}" target="_blank"><i class="fab fa-github gh-icon"></i></a>
              </div>
              {% if talk.github_stars %}
              <span class="stars" data-toggle="tooltip" title="GitHub Stars">
                <i class="fas fa-star"></i>
                <span id="{{ talk.github_stars }}-stars"></span>
              </span>
              {% endif %}
            </div>
            {% endif %}
          </div>
        </div>
      </div>
    </a>
  </div>
{% endfor %}

</div>