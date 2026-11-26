# ceezh.github.io

Source for [ceezh.github.io](https://ceezh.github.io), built with [al-folio](https://github.com/alshedivat/al-folio) v1 (Jekyll).
Every push to `main` triggers GitHub Actions to build the site and publish it to the `gh-pages` branch.

## Layout

```
_config.yml                  site settings (name, fonts, publication options, plugins)
_pages/
  about.md                   home page: bio, news, selected publications, service
  publications.md            all publications, grouped by year
  news.md                    all news items
  404.md
_bibliography/papers.bib     every paper (the single source for both publication lists)
_news/YYYY-MM-DD-slug.md     one file per news item
_data/
  socials.yml                email / Scholar / GitHub / X / CV links
  venues.yml                 venue badge colors
assets/
  img/prof_pic.jpg           profile photo (square crop)
  img/prof_pic_full.jpg      full, uncropped profile photo (kept for later use; not shown on the site)
  img/publication_preview/   paper thumbnails
  pdf/CV_Ce_Zhang.pdf        CV
  css/main.scss              loads al-folio's styles + _sass/_custom.scss
_sass/_custom.scss           site-specific styling (font, photo, news, "see all" links)
_layouts/about.liquid        local override of al-folio's home-page template
_includes/news.liquid        local override of al-folio's news list
_includes/hook/bib.liquid    shows arXiv preprints as "arXiv, 2026" on the publication lists
.github/workflows/deploy.yml build + deploy
Gemfile, Gemfile.lock        Ruby dependencies (al-folio gems, pinned)
purgecss.config.js           strips unused CSS during deploy
docker-compose-slim.yml      optional local preview with Docker
```

Folder names starting with `_` are Jekyll conventions and must keep those names.

## Common edits

| To change…                       | Edit                                                                                        |
| -------------------------------- | ------------------------------------------------------------------------------------------- |
| Bio, research blurb, service     | `_pages/about.md` (text after `<!-- below-publications -->` appears after the papers)       |
| Chinese name after the name      | `native_name` in `_pages/about.md`                                                          |
| Number of news items on home     | `announcements.limit` in `_pages/about.md` (all items are on `/news/`)                      |
| Add a news item                  | new file `_news/YYYY-MM-DD-slug.md` (copy an existing one, change `date:` and the text)     |
| Add a paper                      | `_bibliography/papers.bib` + thumbnail in `assets/img/publication_preview/`                 |
| Show a paper on the home page    | add `selected = {true}` to its BibTeX entry (home order = order in `papers.bib`)            |
| CV                               | replace `assets/pdf/CV_Ce_Zhang.pdf` (keep the file name)                                   |
| Email / Scholar / GitHub / X     | `_data/socials.yml` (shown under the photo and at the bottom of the home page)              |
| Venue badge colors               | `_data/venues.yml` (keys match the `abbr` field in `papers.bib`)                            |
| Colors, fonts, layout details    | `_sass/_custom.scss` (+ the Google Fonts URL under `third_party_libraries` in `_config.yml`) |
| Name, site description, settings | `_config.yml`                                                                               |

### Adding a paper

Every entry in `papers.bib` **must** have `arxiv_year`: the year of the first arXiv version (the first two digits
of the arXiv ID, so `2505.24869` means 2025), or the publication year if the paper is not on arXiv. The publications page
groups papers by this year; `year` stays the publication year shown after the venue. The build fails if an entry has
no `arxiv_year`.

Other useful fields: `abbr` (venue badge), `preview` (thumbnail file name), `abstract`, `arxiv`, `pdf`, `code`, `website`,
`award` + `award_name`, `bibtex_show = {true}`. Mark equal contribution with `*` after the last name (`Zhang*, Ce`) and add
`annotation = {* Equal contribution}`. Full list: [al-folio docs](https://github.com/alshedivat/al-folio/blob/main/docs/CUSTOMIZE.md#adding-a-new-publication).

## Preview locally

With Docker installed:

```bash
docker compose -f docker-compose-slim.yml up
# then open http://localhost:8080
```

Or with Ruby 3.3 + Bundler: `bundle install && bundle exec jekyll serve`, then open http://localhost:4000.

## Deployment (one-time setup)

1. Push to `main`. The **Deploy site** workflow builds the site and pushes the result to the `gh-pages` branch.
2. In the repo on GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch**, branch **`gh-pages`**, folder **`/ (root)`**.

After that, every push to `main` redeploys automatically (takes ~3–5 minutes; check the **Actions** tab if something looks off).

## Local overrides of al-folio files

Layouts and base styles come from the `al_folio_core` gem (pinned in `Gemfile`). This site overrides only:

- `assets/css/main.scss`: the gem's file plus one line, `@use "custom";`
- `_includes/news.liquid`: shows news dates as "Sep 2026" instead of "Sep 01, 2026"
- `_layouts/about.liquid`: shows `native_name` after the name, social icons under the photo (`profile.social`), and renders
  the part of `about.md` after `<!-- below-publications -->` below the selected publications

When bumping `al_folio_core` in `Gemfile`, compare these three files with the new gem version.

`_includes/hook/bib.liquid` is not an override but al-folio's documented hook for the venue line of each paper;
it shortens arXiv preprints to "arXiv, 2026".
