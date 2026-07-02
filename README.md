# benpetito.github.io

Ben's personal blog and portfolio.

## Tech stack

- [Hugo](https://gohugo.io/) (Extended, v0.163.2) — static site generator
- [Toha](https://github.com/hugo-toha/toha) v4.15.0 — theme, pulled in as a Hugo Module
- Node/npm — provides theme assets (flag-icons, @fontsource/mulish, katex) via `hugo mod npm pack`
- GitHub Actions — builds and deploys to GitHub Pages on every push to `main` ([.github/workflows/deploy.yml](.github/workflows/deploy.yml))

## Local development

```sh
hugo mod npm pack
npm install --prefix .
hugo server
```

## Content

- `content/posts/` — blog posts
- `data/en/sections/` — homepage sections (About, Skills, Experience, Recent Posts, Projects), ordered by each section's `weight`
