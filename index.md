---
layout: default
title: Recipes
---

# Recipes

<ul>
{% for recipe in site.recipes %}
  <li><a href="{{ recipe.url | relative_url }}">{{ recipe. Title }}</a></li>
{% endfor %}
</ul>
