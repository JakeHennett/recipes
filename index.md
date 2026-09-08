---
layout: default
title: Recipes
---

# Recipes

<input
  type="text"
  id="search-box"
  placeholder="Search recipes..."
  style="padding: 8px; width: 100%; max-width: 400px; margin-bottom: 1rem;"
>
<div id="search-results"></div>


<ul>
{% for recipe in site.recipes %}
  <li><a href="{{ recipe.url | relative_url }}">{{ recipe.title }}</a></li>
{% endfor %}
</ul>
