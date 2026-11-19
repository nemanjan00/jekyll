# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Personal Jekyll blog served at https://nemanja.top. The GitHub repo is `nemanjan00/jekyll`.

## Deploy

The site is hosted on **Render** (`rndr-id` response header) behind Cloudflare (`s-maxage=300`). Render builds from `master` with the committed `Gemfile`/`Gemfile.lock`. There are no CI workflows in the repo. When auto-deploy doesn't trigger, the fix is in the Render dashboard ("Manual Deploy → Deploy latest commit"). Check the live origin with a cache-busting query (`curl -s "https://nemanja.top/?v=$(date +%s)"`).

## Build / preview

The system Ruby is much newer than `.ruby-version` (3.0.0), and the locked Jekyll 4.2 / sassc gems target 3.0. Build in a matching container:

```sh
docker run --rm -v "$PWD":/srv -w /srv ruby:3.0 sh -c 'bundle install --quiet && bundle exec jekyll build -d /tmp/s'
```

`.bundle/config` sets `BUNDLE_PATH: vendor/bundle`, so the container writes root-owned `vendor/` and `.jekyll-cache/` into the repo (both gitignored). Remove them through docker (`... ruby:3.0 rm -rf vendor .jekyll-cache`), because a plain `rm` fails with permission denied. To preview, build to a directory, serve it with `python -m http.server`, and take screenshots with `chromium --headless --screenshot`.

No tests or linting.

## Architecture

- **No gem theme.** The look is a hand-written Dracula theme: `_layouts/` (`default` → `blog`, `post`, `page`), `_includes/` (`head`, `header`, `footer`, `reading-time`, `footer-scripts` = Google Analytics), and a single `assets/css/main.scss` that defines the Dracula palette variables and the Rouge syntax colors. The site is dark-only by design.
- `bulma-clean-theme` and `minima` are still in the `Gemfile` but unused. They were kept so that Render's lockfile and build stay unchanged. `jekyll-seo-tag` and `jekyll-sitemap` come in as dependencies of bulma-clean-theme, so removing that gem means adding them to the `Gemfile` directly.
- `index.html` uses `layout: blog`, which uses `jekyll-paginate` (5 posts per page, `/pageN/`). Pagination only works in `index.html`.
- Permalinks are `/:categories/:year/:month/:day/:title` with no `.html` in canonical URLs. Keep them stable, because existing links depend on them.

## Posts

Posts are files in `_posts/YYYY-MM-DD-slug.md`, and their images go in `assets/`. The usual front matter is:

```yaml
layout: post
title: "..."
published: true
author: nemanjan00
image: /assets/foo.jpg        # card thumbnail on the index
hero_image: /assets/foo.jpg   # dimmed background behind the post title
```

`show_sidebar` and `hero_darken` are left over from the old Bulma theme and are ignored. The index excerpt is the post's first paragraph.
