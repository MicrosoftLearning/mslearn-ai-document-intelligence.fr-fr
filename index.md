---
title: Exercices sur Azure AI Intelligence documentaire
permalink: index.html
layout: home
---

# Exercices sur Azure AI Intelligence documentaire

Les exercices suivants sont conçus pour prendre en charge les modules sur Microsoft Learn.


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
