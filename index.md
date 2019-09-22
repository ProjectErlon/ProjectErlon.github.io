---
title: 'CodeLab'
description: 'Сборник всех наших "проектов"'
layout: default
---

<h1 class="uk-article-title"><a class="uk-link-reset" href="">Heading</a></h1>
# Добро пожаловать

<ul>
{% for page in site.pages %}
  <li><a href="{{ page.url }}">{{ page.title }}</a></li>
{% endfor %}
</ul>
