# Repository Guidelines

## Project Structure & Module Organization

This is a Jekyll Academic Pages site for `zhouzhoushen.github.io`. Site-wide settings live in `_config.yml`, navigation and author data live in `_data/`, reusable Liquid templates live in `_layouts/` and `_includes/`, and Sass partials live in `_sass/`. Public pages are in `_pages/`; dated blog posts are in `_posts/`; academic collections are in `_publications/`, `_talks/`, `_teaching/`, and `_portfolio/`. Static files belong in `files/`, images in `images/`, and browser assets in `assets/`. Publication and talk generators are in `markdown_generator/`; CV conversion helpers are in `scripts/`.

## Build, Test, and Development Commands

- `bundle install`: install Ruby and Jekyll dependencies from `Gemfile`.
- `bundle exec jekyll serve -l -H localhost`: run a local preview at `http://localhost:4000` with live reload.
- `bundle exec jekyll build`: build the static site into `_site/` for validation.
- `docker compose up`: run the Docker-based Jekyll preview on port `4000`.
- `npm install && npm run build:js`: install JavaScript tooling and regenerate `assets/js/main.min.js` after editing source JS.
- `bash scripts/update_cv_json.sh`: regenerate `_data/cv.json` from `_pages/cv.md`.

## Coding Style & Naming Conventions

Use two-space indentation in YAML front matter, Liquid, HTML, and SCSS to match the existing files. Keep Markdown pages front-matter first, then content. Name collection entries with date-prefixed, lowercase slugs, for example `_posts/2026-01-15-topic.md` or `_talks/2026-03-01-event.md`. Keep generated output, caches, and dependencies out of commits unless intentionally updating tracked artifacts such as `assets/js/main.min.js` or `talkmap_out.ipynb`.

## Testing Guidelines

There is no dedicated unit test suite. Treat `bundle exec jekyll build` as the required smoke test before committing content, layout, Sass, or config changes. For JavaScript edits, run `npm run build:js` and verify the Jekyll build still succeeds. For talk-map changes, ensure the notebook workflow can execute or that generated `talkmap_out.ipynb` changes are intentional.

## Commit & Pull Request Guidelines

The current history uses short messages such as `update`; prefer concise imperative subjects with a clear scope, for example `Update CV data` or `Fix publication permalink`. Pull requests should summarize the visible site change, list validation commands run, link relevant issues, and include screenshots for layout, theme, or navigation changes.

## Agent-Specific Instructions

Preserve user content and avoid broad template rewrites unless requested. Check `git status` before editing, because this repository may already contain unrelated local modifications. Do not modify personal profile data, publications, or generated files unless the task explicitly calls for it.
