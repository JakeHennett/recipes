# Jake's Recipes

A Markdown-based recipe collection, versioned in Git and published as a static site with Jekyll on GitHub Pages.

**Live site:** https://jakehennett.github.io/recipes/

Recipes are plain Markdown files with YAML frontmatter. That keeps them readable on their own, editable in any text editor or in Obsidian, and portable to a different site generator later without touching the recipe files themselves.

---

## Current status

Working:

- Jekyll builds and deploys on push to `main`
- `_recipes/` is a proper Jekyll collection, output as individual pages
- The home page lists every recipe and links to it correctly
- The `recipe` layout renders title, servings, prep and cook time, ingredients, and steps

Known cleanup items are listed under [Outstanding items](#outstanding-items).

---

## TODO:

- Update formatting to match Google Drive recipe cards
- Add search bar, make search bar visible across all pages.
- Add source field to template and display on pages.
- Tie source to ISBN and book title if possible.
- Give an option to select tags and only display recipes with that tag.
- Find a way to bulk import from old Obsidian vault (https://github.com/JakeHennett/obsidian-vault-public).
- Set up Obsidian on this repo such that recipes can be edited locally and synced.
- Determine if pages can be easily exported to a physically printable format.
- Create a _books folder where referenced cookbooks can have their metadata stored.

---

## Repository structure

```
/
├── _config.yml            # Site + collection configuration
├── index.md               # Home page, lists all recipes
├── _recipes/              # One Markdown file per recipe (the collection)
│   ├── example.md
│   ├── nycheesecake.md
│   └── template.md        # Should be moved out, see Outstanding items
├── _layouts/
│   └── recipe.html        # Renders a single recipe
├── _data/
│   └── navigation.yml     # Present but not currently wired into anything
├── .gitignore
└── README.md
```

Note that the collection folder is `_recipes/` with a leading underscore. Jekyll requires the underscore for collection source folders. The published URLs do not contain it.

---

## Recipe format

Every recipe is a file in `_recipes/`. The filename becomes the URL slug, so `nycheesecake.md` publishes to `/recipes/nycheesecake/`.

```yaml
---
layout: recipe
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
(Optional freeform notes, tips, variations, substitutions, photos.)
```

Field notes:

| Field | Rendered today | Purpose |
|---|---|---|
| `title` | Yes | Page heading and home page link text. Required. |
| `servings` | Yes | Shown in the metadata block |
| `time.prep` / `time.cook` | Yes | Shown in the metadata block |
| `ingredients` | Yes | Rendered as a list |
| `steps` | Yes | Rendered as a numbered list |
| `description` | Not yet | Reserved for cards and search results |
| `tags` | Not yet | Reserved for filtering and browse-by-tag |
| `source` | Not yet | Where the recipe came from |
| `rating` | Not yet | Personal rating |

The unrendered fields are safe to fill in now. They are read by the upgrades described below.

---

## Adding a recipe

1. Copy the frontmatter template above into a new file in `_recipes/`.
2. Name the file with the slug you want in the URL, lowercase, no spaces.
3. Fill in at minimum `title`, `ingredients`, and `steps`.
4. Commit and push to `main`. GitHub Pages rebuilds automatically, usually within a minute.

---

## Configuration

```yaml
title: Jake's Recipes
theme: minima
baseurl: "/recipes"

collections:
  recipes:
    output: true
    permalink: /:name/
```

Two things about this file are easy to break and worth understanding, because both have already caused outages in this repo.

**`baseurl` and `permalink` are a matched pair.** This is a GitHub Pages project site, so it is served from `https://jakehennett.github.io/recipes/`, not from a domain root. `baseurl: "/recipes"` tells Jekyll about that prefix. The collection `permalink` must therefore be `/:name/` and must *not* repeat `/recipes/`, or every URL gets a doubled segment and 404s. Correspondingly, links in templates must pass through `relative_url`, which is what applies the prefix:

```liquid
<a href="{{ recipe.url | relative_url }}">{{ recipe.title }}</a>
```

Use `relative_url` exactly once per link. Applying it to a path that already contains `/recipes` is the other way to produce the doubled segment.

**YAML indentation is significant.** `recipes` must be indented under `collections`, and `output` and `permalink` must be indented further under `recipes`. If all three end up at the same level, YAML reads them as siblings, `output: true` never reaches the collection, no recipe pages are generated, and every link 404s while the home page still renders normally. That failure mode looks nothing like an indentation problem, so check the nesting first.

### Recommended addition

Add a `defaults` block so recipes do not need a `layout` line at all:

```yaml
defaults:
  - scope:
      path: ""
      type: "recipes"
    values:
      layout: "recipe"
```

Without this, a recipe file that omits `layout: recipe` silently publishes as an unstyled page with no title and no site header. `nycheesecake.md` is currently in that state. The `defaults` block fixes it and prevents it for every recipe added from now on.

---

## Local development

Optional, but useful for previewing changes before pushing. Add a `Gemfile`:

```ruby
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
```

Then:

```bash
bundle install
bundle exec jekyll serve
```

The local site is at **http://localhost:4000/recipes/**, including the `baseurl` prefix, which matches production. Serving at the bare root is what hides baseurl bugs, so keep the prefix.

---

## Troubleshooting

**Links 404 right after a fix.** GitHub Pages serves HTML with a short cache lifetime, so your browser may still hold the previous build's home page and its old hrefs. Hard reload with `Ctrl+Shift+R`, or check in a private window, before assuming the repo is still broken.

**A recipe page renders with no title or header.** It is missing `layout: recipe`. Add the `defaults` block above.

**A recipe shows as a blank bullet on the home page.** Its `title` is empty.

**Every link 404s but the home page looks fine.** Check `_config.yml` indentation, then check for a doubled `/recipes/` segment.

---

## Outstanding items

- [ ] Move `_recipes/template.md` out of the collection. While it lives there it publishes as a recipe and shows up as a blank bullet on the home page. Move it back to the repo root and add `published: false`, or rename the folder to `_templates/`.
- [ ] Add the `defaults` block, then remove the now-redundant `layout: recipe` lines from individual recipes if you like.
- [ ] Fill in `nycheesecake.md`, which currently has a title and empty everything else.
- [ ] Decide whether `_data/navigation.yml` is wanted. Minima builds its header from site pages, so the file is inert as-is. Either wire it into a custom header or delete it.

---

# Optional upgrades

Three independent tracks, roughly in increasing order of effort. Each is a high-level plan rather than a step-by-step, and none of them require changing the recipe files.

## 1. UI and formatting

**Goal:** move from Minima's default blog styling to something that reads like a cookbook, on both phone and desktop.

**Why it is low risk:** Minima supports style overrides without forking the theme, so this is additive.

**Gameplan:**

1. Create `assets/css/style.scss` beginning with the Minima import, then add your own rules below it. This is the supported override hook and survives theme updates.
2. Rebuild the home page as a card grid instead of a bullet list. One card per recipe showing title, `description`, total time, and `tags`. This is where the currently-unrendered frontmatter fields start paying off.
3. Improve the recipe layout itself: a metadata bar for servings, times, rating, and source; ingredients in a scannable column; steps with more vertical breathing room and larger tap targets.
4. Add tag chips that link to filtered views, which sets up naturally for the search work in track 2.
5. Add a print stylesheet so a recipe prints on one page without site navigation. This matters more than it sounds for a recipe site.
6. Optionally add checkbox states to ingredients so you can tick them off while cooking. Keep it in memory only, no persistence needed.

**Effort:** an afternoon for steps 1 through 3, which is where most of the visible gain is.

## 2. Search bar

**Goal:** type-to-filter across recipe titles, tags, and ingredients, entirely client-side. No server, no external service.

**Key idea:** Jekyll can generate a JSON index of the collection at build time, and a small amount of JavaScript filters it in the browser. Because the whole collection is a handful of files, this stays fast without a search library.

**Gameplan:**

1. Add a `search.json` file at the repo root that loops over `site.recipes` and emits title, url, description, tags, and ingredients for each. Give it `layout: null` so Jekyll writes raw JSON.
2. Add a search input to the home page.
3. On page load, fetch `search.json` once and hold it in memory.
4. On each keystroke, filter by substring match across the indexed fields and re-render the result list. Debounce lightly if it ever feels jumpy.
5. Show all recipes when the box is empty, so search degrades into the normal index.
6. If the collection grows past a few hundred recipes, or you want typo tolerance and relevance ranking, swap the filter step for Lunr.js. The index from step 1 feeds it directly, so this is a contained change.

**Effort:** steps 1 through 5 are a single sitting. Worth doing after track 1, since the result rendering shares code with the card grid.

## 3. Obsidian vault integration

**Goal:** author and edit recipes in Obsidian on desktop and mobile, with changes syncing to GitHub and publishing automatically.

**Key idea:** the repo is already a valid Obsidian vault, because Obsidian operates on a folder of Markdown files. The work is in the details of not letting the two tools fight over conventions.

**Gameplan:**

1. Open the local clone of this repo as a vault in Obsidian. Nothing needs to move.
2. Add `.obsidian/` to `.gitignore` if you want per-device settings, or commit it deliberately if you want shared settings across your machines. Pick one, because mixing the two causes conflicts.
3. Install the Obsidian Git community plugin and configure periodic pull, commit, and push. This is what turns saving a note into a site deploy.
4. Set up a template so new recipes get the right frontmatter automatically. Point Obsidian's Templates setting at `template.md`, which also gives that file a purpose once it moves out of `_recipes/`.
5. Configure the attachments folder to a Jekyll-visible path such as `assets/images/`, so pasted photos land somewhere the site can serve rather than in a folder Jekyll ignores.
6. Watch two compatibility details:
   - Obsidian wikilinks `[[like this]]` do not render in Jekyll. Use standard Markdown links in anything published, or add a plugin that converts on save.
   - Obsidian's Dataview plugin reads the same YAML frontmatter the site uses, so you get queryable views in the vault for free. Dataview code blocks will not render on the published site, so keep them out of recipe bodies, or in a note that lives outside `_recipes/`.
7. Optionally use the vault as the writing surface and the site purely as the read surface, which avoids ever editing recipes through the GitHub web UI and the indentation accidents that come with it.

**Effort:** an hour for steps 1 through 4. Step 6 is ongoing discipline rather than setup.

---

## Design principles

Worth keeping in mind before adding anything to this project:

- **Recipe files stay portable.** No site-specific syntax in `_recipes/`. If a feature requires special markup inside recipe bodies, put it in a layout or a data file instead.
- **Frontmatter is the schema.** Add fields freely. Unrendered fields cost nothing and become useful later.
- **The site is disposable, the recipes are not.** Jekyll, Minima, and every upgrade above can be replaced. The Markdown files are the actual asset.
