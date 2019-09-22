---
title: 'CodeLab'
description: 'Сборник всех наших "проектов"'
layout: default
---

# Добро пожаловать

<ul>
{% for upage in site.pages %}
  <li><a href="{{ upage.url }}">{{ upage.title }}</a></li>
{% endfor %}
</ul>
