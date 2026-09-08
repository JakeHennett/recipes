# recipes

Here’s a clean, **copy‑paste‑ready summary** you can drop straight into your repo’s README — something you can paste into a new chat later and I’ll immediately know how to continue the setup. It includes **all critical details**, no fluff, and is structured so you can pick up the project at any point.

---

# 📚 Jekyll Recipe Collection — Project Summary

This repo is a **Markdown‑based recipe collection** designed to be:

- Editable in **Obsidian**
- Stored and versioned in **GitHub**
- Viewable through **GitHub Pages (Jekyll)**
- Future‑proof for migration to a **runtime viewer** (Next.js, Astro, Eleventy, etc.)

Recipes use **YAML frontmatter** for structured metadata, making them compatible with Jekyll, Obsidian Dataview, and modern web frameworks.

---

## 🧩 Recipe Format (YAML Frontmatter Template)

Use this template for every new recipe:

```markdown
---
title: ""
description: ""
tags: []
servings: ""
time:
  prep: ""
  cook: ""
ingredients:
  - ""
steps:
  - ""
source: ""
rating: ""
---

## Notes
(Optional freeform notes, tips, variations, substitutions, photos, etc.)
```

This format is portable across all future viewers.

---

## 📁 Required Jekyll Structure

Add these files/folders to the repo:

```
/
├── _config.yml
├── _layouts/
│   └── recipe.html
├── recipes/
│   └── example.md
└── index.md
```

### `_config.yml`

```yaml
title: Jake’s Recipes
theme: minima

collections:
  recipes:
    output: true
    permalink: /recipes/:name/
```

### `_layouts/recipe.html`

```html
---
layout: default
---

<article class="recipe">
  <h1>{{ page.title }}</h1>

  <div class="meta">
    {% if page.servings %}<p><strong>Servings:</strong> {{ page.servings }}</p>{% endif %}
    {% if page.time.prep %}<p><strong>Prep:</strong> {{ page.time.prep }}</p>{% endif %}
    {% if page.time.cook %}<p><strong>Cook:</strong> {{ page.time.cook }}</p>{% endif %}
  </div>

  <h2>Ingredients</h2>
  <ul>
    {% for item in page.ingredients %}
      <li>{{ item }}</li>
    {% endfor %}
  </ul>

  <h2>Steps</h2>
  <ol>
    {% for step in page.steps %}
      <li>{{ step }}</li>
    {% endfor %}
  </ol>

  <div class="content">
    {{ content }}
  </div>
</article>
```

### `recipes/example.md`

```markdown
---
layout: recipe
title: "Example Recipe"
tags: ["demo"]
servings: 2
time:
  prep: "10 minutes"
  cook: "20 minutes"
ingredients:
  - "1 cup water"
  - "1 pinch salt"
steps:
  - "Boil water."
  - "Add salt."
---

## Notes
Demo recipe.
```

### `index.md`

```markdown
---
layout: default
title: Recipes
---

# Recipes

<ul>
{% for recipe in site.recipes %}
  <li><a href="{{ recipe.url }}">{{ recipe.title }}</a></li>
{% endfor %}
</ul>
```

---

## 🚀 Enable GitHub Pages

1. Go to **Settings → Pages**
2. Set **Source** to “Deploy from branch” or “GitHub Actions”
3. GitHub will automatically build the Jekyll site
4. Your recipe collection becomes available at:

```
https://<your-username>.github.io/recipes/
```

---

## 🔮 Future‑Proofing

This setup ensures:

- Recipes remain editable in Obsidian  
- Jekyll builds a simple, automatic viewer  
- You can later migrate to a runtime viewer **without changing recipe files**  
- The repo acts as your permanent, structured recipe database  

---

## 🧭 Continue Setup Later

Paste this summary into a new chat and ask for:

- **Add Jekyll structure**  
- **Help me style the site**  
- **Add search + tags**  
- **Plan runtime viewer migration**  

This README block is everything needed to resume the project at any stage.
