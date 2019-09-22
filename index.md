---
title: 'CodeLab'
description: 'Сборник всех наших "проектов"'
layout: default
---

<h1 class="uk-article-title"><a class="uk-link-reset" href="">Heading</a></h1>
# Добро пожаловать

<ul>
{% for upage in site.pages %}
  <li><a href="{{ upage.url }}">{{ upage.title }}</a></li>
{% endfor %}
</ul>
