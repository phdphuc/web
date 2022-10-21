---
layout: page
permalink: /publications/
title: publications
description: publications
years: [2022,2021,2019,2018,2017,2014]
nav: true
---

<div class="publications">

{% for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}

</div>
