# Repository Guidelines

## Project Structure

- `hugo.toml`: main Hugo configuration (baseURL, modules, build stats, outputs).
- `config/_default/*.toml`: language menus, params, and module configuration.
- `content/{chinese,english,deutsch}/`: site content (Markdown pages and blog posts).
- `themes/bigspring-light/`: theme source
  - `themes/bigspring-light/layouts/`: Hugo Go templates and partials.
  - `themes/bigspring-light/assets/`: SCSS/JS and vendor assets used by Hugo Pipes.
- `assets/`: site-level assets mounted into Hugo (see `[module.mounts]` in `hugo.toml`).
- Generated outputs: `public/` (build output), `resources/` (Hugo cache), `hugo_stats.json` (build stats for CSS purging).

## Build, Test, and Development Commands

Prereqs: Hugo Extended (CI uses `0.124.1`), Go `1.20.x`, Node `18.16.1`.

- `npm install`: install Node dev dependencies (PostCSS, Prettier).
- `npm run dev`: run local server (`hugo server`).
- `npm run preview`: production-like server (minified, watches, disables fast render).
- `npm run build`: production build into `public/` (includes build stats used by PurgeCSS).
- `npm run update-modules`: refresh Hugo modules (`hugo mod get -u ./...` + tidy).
- `npm run project-setup`: one-time setup for this repo’s configuration/assets.

## Coding Style & Naming

- Indentation: 2 spaces (see `.editorconfig`).
- Templates: `themes/**/layouts/**/*.html` are Go templates; format with Prettier + `prettier-plugin-go-template`.
  - Example: `npx prettier -w "themes/**/*.html"`.
- Content: keep front matter consistent (title/date/draft) and prefer kebab-case filenames (e.g., `content/chinese/blog/my-post.md`).

## Testing Guidelines

No dedicated automated test suite is configured. Validate changes by:

- Running `npm run dev` and checking affected pages.
- Running `npm run build` to catch Hugo/template/module errors and ensure CSS purging stays correct.

## Commit & Pull Request Guidelines

- Commits in history are mostly short, descriptive subjects (often Chinese), with occasional `feat:`-style prefixes. Use clear, imperative summaries; add a prefix like `feat:`/`fix:` when it helps.
- PRs: describe the change, link relevant issues, and include screenshots for layout/style/content changes (especially anything under `themes/` or `assets/`).

## Agent Notes

- Avoid hand-editing generated directories (`public/`, `resources/`) unless the goal is to commit regenerated output.
- Prefer minimal, focused diffs; keep content edits and theme/layout edits in separate commits when practical.
