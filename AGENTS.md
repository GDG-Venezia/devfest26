# AGENTS.md — DevFest Venezia 2026 website

Hugo site for DevFest Venezia 2026, deployed to https://devfest26.gdgvenezia.it by GitHub Pages
on every push to `main`.

## Two repos: decide where a change belongs first

The design and all behaviour live in the shared theme
[GDG-Venezia/devfest-hugo-theme](https://github.com/GDG-Venezia/devfest-hugo-theme), included
here as a git submodule in `themes/devfest-hugo-theme`. Other editions use the same theme.

| Change | Goes in |
| --- | --- |
| Text, dates, sessions, speakers, sponsors, travel info, links, images for 2026 | **this repo**: `config.toml`, `data/`, `content/`, `static/` |
| Layouts, CSS, JS, components, section order, new behaviour, new or renamed data fields | **the theme** (`themes/devfest-hugo-theme`) |

- Do not add `layouts/`, `assets/` or other theme overrides in this repo. If something must look or
  behave differently, change the theme, usually behind an optional data field.
- If a request mixes both (for example "add a field and fill it for 2026"), do the theme part first,
  then the site part.
- Always tell the user when a request turned into a theme change: it affects every edition.

## Workflow for a theme change

1. Work inside the submodule, which is its own git repo:
   ```bash
   git -C themes/devfest-hugo-theme switch main
   git -C themes/devfest-hugo-theme pull
   ```
   (A fresh submodule is on a detached HEAD: commits made there without switching are easily lost.)
2. Make the change in `themes/devfest-hugo-theme`. Follow that repo's `AGENTS.md`.
3. Preview this site with `hugo server` and check desktop and phone widths.
4. Commit and push in the theme repo (only when the user asks to push).
5. Bump the submodule here and commit it:
   ```bash
   git add themes/devfest-hugo-theme
   git commit -m "Bump theme: <what changed>"
   ```
6. Pushing this repo deploys the live site. Push only when the user asks.

Never push this repo with a submodule pointer to a theme commit that is not on GitHub: the deploy
would fail to check it out. Push the theme first.

There may also be a standalone checkout of the theme next to this repo (`../devfest-hugo-theme`),
used with `hugo server --themesDir ..`. Pull before editing and change only one of the two checkouts
per task, otherwise commits end up split across clones.

To pick up theme changes made elsewhere: `git submodule update --remote themes/devfest-hugo-theme`,
check the site, commit the bump.

## Checks before handing off

- `hugo --gc --minify` builds without errors. CI uses Hugo **0.152.2** extended
  (`.github/workflows/gh-pages.yml`): don't rely on features from newer Hugo versions (for example
  `hugo.Data`; keep `site.Data`).
- Preview the pages you touched at desktop and ~375px width.
- Live features can be previewed with `/?now=<date>T11:00:00%2B02:00` once `data/sessions.yml` is enabled.

## Content rules

- Facts on the site (travel, schedules, sponsors, links) must come from a source the user gave or
  that you verified; say what you could not verify. Never publish invented speakers or sessions.
- `data/sessions.yml` stays `enable: false` until the programme is public; the site shows
  `data/comingSoon.yml` instead.
- Every data file and field is documented in the theme README and shown in the theme's `exampleSite/`.

## Related

- `../devfest25` (DevFest 2025) uses the same theme locally as an **offline preview only**: never
  commit or push changes there.
