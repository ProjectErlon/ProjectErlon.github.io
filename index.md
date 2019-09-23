---
title: 'CodeLab'
description: 'Сборник всех наших "проектов"'
layout: default
---

# Добро пожаловать



<ul>
{% for upage in site.documents %}
  <li><a href="{{ upage.url }}">{{ upage.title }}</a> - {{ upage.categories }}</li>
{% endfor %}
</ul>



<ul>
{% for upage in site['arduino'] %}
  <li><a href="{{ upage.url }}">{{ upage.title }}</a></li>
{% endfor %}
</ul>



{% for collection in site.collections %}
  <h2>Items from {{ collection.label }}</h2>
  <ul>
    {% for item in site[collection.label] %}
      <li><a href="{{ item.url }}">{{ item.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}
