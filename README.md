# notes-public

Short notes and technical writeups I want to keep around.

This repository is built as a static site with MkDocs and published through GitHub Pages. The source lives in `docs/`, the site configuration lives in `mkdocs.yml`, and the generated site is built in CI and deployed from GitHub Actions.

The intent is simple:

- keep the notes in plain Markdown
- render them as a browsable site
- publish updates automatically when changes land on `main`

## Publishing model

GitHub Actions builds the site and deploys it to GitHub Pages.

- source files are committed to the repository
- generated output in `site/` is not committed
- the workflow in `.github/workflows/pages.yml` handles the build and deployment

## Local development

Use `uv` for local setup and site generation.

Create or refresh the virtual environment:

```bash
uv venv
```

Install dependencies:

```bash
uv pip install -r requirements.txt
```

Serve the site locally:

```bash
uv run mkdocs serve
```

Build the static site:

```bash
uv run mkdocs build
```

## Content

Current notes:

- [Caps Lock to Hyper with Karabiner-Elements](docs/posts/caps-lock-to-hyper.md)
- [Quick Window Switching with Hammerspoon](docs/posts/quick-window-switch-hammerspoon.md)

