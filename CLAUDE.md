# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

When writing or reviewing documentation, follow the guidance in
`skills/n8n-docs-author/SKILL.md`. `STYLE_GUIDE.md` (repo root) is the source of
truth for writing style; the skill is a distillation of it.

## What this repo is

This is the source for [docs.n8n.io](https://docs.n8n.io/), the documentation
site for n8n (a workflow automation tool). It's a **MkDocs site** built with the
**Material for MkDocs** theme, not application code. Almost all content lives in
`docs/` as Markdown. There is no compiled application and no test suite — the
"build" is `mkdocs build`, and quality gates are link checking and prose
linting.

## Common commands

```bash
# Install dependencies (use a virtualenv; Python 3.12, see runtime.txt)
pip install -r requirements.txt
pip install mkdocs-material          # external contributors (free theme)
# pip install _submodules/insiders   # n8n org members only (Insiders theme)

# Serve a local preview with strict warnings-as-checks
mkdocs serve --strict

# Faster local previews:
mkdocs serve --strict --dirty            # only rebuild changed files
export NO_TEMPLATE=true && mkdocs serve  # skip fetching live template/workflow data

# Full production build (what Netlify runs)
mkdocs build --strict

# Prose linting (install the Vale CLI separately: https://vale.sh)
vale --glob="*.md" docs
```

There is no `npm`, no unit tests, and no linter beyond Vale/Lexi/lychee.
`--strict` turns MkDocs warnings (broken nav entries, bad internal links) into
build failures, so prefer it — it mirrors CI.

## Build architecture

The site is configured across three files that work together:

- **`mkdocs.yml`** — theme, Markdown extensions, and plugins. It does **not**
  contain the navigation; it ends with `INHERIT: ./nav.yml`.
- **`nav.yml`** — the entire site navigation tree (~100KB). Any new page must be
  added here or it won't appear in the nav (and `--strict` warns about omitted
  files).
- **`main.py`** — the `mkdocs-macros-plugin` module. It defines Jinja macros
  (`templatesWidget`, `workflowDemo`) callable from Markdown. Note macros use
  **custom delimiters** (`[[ ... ]]` and `[[% ... %]]`, not `{{ }}`) so they
  don't clash with literal braces in docs. These macros fetch live data from
  `api.n8n.io`; `NO_TEMPLATE=true` short-circuits them with placeholders.

### Key conventions baked into the build

- **Admonitions use the experimental block syntax** `/// note | Title … ///`,
  not the Material `!!!` default. Collapsibles use `??? note "Title"`.
- **Snippets** are shared content reused across pages via the `pymdownx.snippets`
  syntax `--8<-- "_snippets/path/file.md"`. Reusable fragments live in
  `_snippets/`. The glossary (`_glossary/main.md`) is auto-appended to every page.
- **Code blocks must be indented with tabs, not spaces.** This is enforced via
  `preserve_tabs` in SuperFences and matters because code samples may be copied
  into nodes, where the n8n node linter requires tabs. `.editorconfig` enforces
  this — don't let your editor convert tabs to spaces.
- **External links** must open in a new tab:
  `[text](url){:target="_blank" .external-link}`. The `privacy` plugin also adds
  `target="_blank"` to nav links automatically.
- `_overrides/` holds theme template overrides; `_extra/` (under `docs/`) holds
  custom CSS/JS; `_yaml/data-functions.yml` provides data to macros.

## Directory map (the non-obvious parts)

- `docs/` — all published content. Subdirs mirror the site IA (e.g.
  `integrations/`, `hosting/`, `workflows/`, `code/`, `advanced-ai/`).
- `docs/integrations/builtin/{app-nodes,trigger-nodes,core-nodes,cluster-nodes,credentials}/`
  — node reference pages. App/trigger node files are named
  `n8n-nodes-base.<node-name>.md`. See `CONTRIBUTING.md` for the required
  structure and `document-templates/` for the starting templates per page type.
- `document-templates/` — canonical templates (app-nodes, core-nodes,
  credentials, feature, tutorial, release-notes, etc.). Start new pages from these.
- `_snippets/` — reusable content fragments included via `--8<--`.
- `styles/` — Vale configuration: `n8n-styles/` (custom rules),
  `from-microsoft/` and `from-write-good/` (vendored rule sets), and
  `config/vocabularies/default/{accept,reject}.txt` (add brand/term spellings to
  `accept.txt` to silence false positives).
- `_doctools/` — standalone maintenance scripts (`pageinfo.py`,
  `change_link_style.py`), not part of the build.
- `archive/` and `docs-site-feature-tests/` — out of the published flow.

## CI and quality gates (`.github/workflows/`)

- `lint.yml` — runs **Vale** on `docs` and `_snippets` via reviewdog. Currently
  non-blocking (false positives), but fix what you reasonably can.
- `lexi.yml` — **Lexi** readability report posted to the PR. Target a combined
  score of 60+; aim higher. Shorter sentences and simpler words improve it.
- `lychee-pr-check.yml` / `lychee.yml` — external **link checking** on changed
  Markdown (config in `lychee.toml`).
- `auto-handle-on-label.yml` / `support.yml` — issue/PR triage automation.

Netlify builds with `pip install -r requirements.txt && mkdocs build --strict`
and publishes `site/`.

## Contribution notes

- Changes target the `main` branch via PR; PRs get an automatic Netlify preview.
- Quality matters: low-effort or AI-slop PRs are labeled and closed (see
  `CONTRIBUTING.md`). Test that your content builds and that links resolve.
- This repo is documentation only. Product bugs belong in the
  [n8n product repo](https://github.com/n8n-io/n8n); questions go to the forum.
</content>
</invoke>
