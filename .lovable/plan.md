
# Embedded Systems Portfolio — Samuel Gathemia

Target deploy: `https://samuelgathemia.github.io/samuelgathemia/` (Vite base `/samuelgathemia/`).

## 1. Stack change (required for GitHub Pages)

The template ships TanStack Start (SSR + Cloudflare Worker), which GitHub Pages cannot host. I'll convert it to a plain **Vite + React + TypeScript SPA** with **React Router**, keeping Tailwind v4, shadcn primitives, and existing tooling.

- Remove: `src/router.tsx`, `src/routes/*`, `src/routeTree.gen.ts`, `src/server.ts`, `src/start.ts`, TanStack Start plugin from `vite.config.ts`, `@tanstack/react-start`, `@tanstack/react-router` deps.
- Add: `react-router-dom`, standard `index.html` + `src/main.tsx` + `src/App.tsx`.
- `vite.config.ts`: `base: "/samuelgathemia/"`, keep `@tailwindcss/vite`.
- Add `public/404.html` (SPA fallback trick) and `public/.nojekyll`.
- GitHub Actions workflow `.github/workflows/deploy.yml` to build and deploy to `gh-pages`.

## 2. Design system (`src/styles.css`)

Dark-only theme with the specified palette, JetBrains Mono headings/code, Inter body. Load fonts via `<link>` in `index.html` (Tailwind v4 rule). Semantic tokens: `--background #0D1117`, `--card #161B22`, `--border #30363D`, `--foreground #E6EDF3`, `--muted-foreground #8B949E`, `--primary` cyan `#22D3EE`, `--accent` emerald `#10B981`. Register in `@theme inline`. Utilities for terminal cursor, subtle PCB trace SVG background, hover elevation.

## 3. Content data files (`src/data/`)

All placeholder content lives here so it's edited without touching components:

- `profile.ts` — name "Engineer Samuel Gathemia", title, statement, socials (GitHub, LinkedIn, email, Nairobi).
- `skills.ts` — categorized (Languages, Platforms, Frameworks, Protocols, Networking, Tools, VCS).
- `projects.ts` — 7 projects (SmartLights, ESP32 Smart Lighting Controller, FreeRTOS Message Bus, Wi-Fi Manager, UART CLI Framework, HTTP Server, OTA Update System, Sensor Dashboard) with slug, description, stack, highlights, problem, objectives, architecture, hardware, software, directory tree, code snippet, lessons, future work, repo URL.
- `philosophy.ts` — engineering principles.
- `blog.ts` — 5 article stubs (title, date, reading time, tags, preview, body markdown-lite).
- `experience.ts` — timeline entries (Education, IEEE, Industrial Attachment, Research, Projects, Leadership).
- `certifications.ts` — placeholder cards.

## 4. Routes

```
/                      Home (hero, about, skills, featured projects, philosophy, blog preview, timeline, GitHub stats, certifications, contact)
/projects              All projects grid
/projects/:slug        Project detail page (Overview → Future Improvements)
/blog                  Article index
/blog/:slug            Article view
/resume                Preview + download
*                      Custom 404
```

## 5. Components (`src/components/`)

Layout: `Navbar` (sticky, animated mobile menu), `Footer`, `PCBBackground` (subtle SVG traces), `SectionHeading`, `TerminalPrompt`.
Sections: `Hero`, `About`, `Skills` (categorized chips + animated bars), `FeaturedProjects`, `Philosophy`, `BlogPreview`, `Timeline`, `GitHubStats`, `Certifications`, `Contact` (form is mailto: — no backend), `ScrollProgress`.
Project detail: `ProjectHeader`, `ArchitectureDiagramPlaceholder`, `CodeBlock` (simple `<pre>` with copy button), `InfoGrid`.

## 6. GitHub API integration

Client-side `useEffect` fetch `https://api.github.com/users/samuelgathemia/repos?sort=updated&per_page=6` in `GitHubStats`. Also embed `github-readme-stats` images (contribution graph, stats card, top languages) — zero-dep, cached by GitHub. Graceful loading + error fallback, no token required.

## 7. SEO & assets

- `index.html` with meta description, Open Graph, Twitter card, JSON-LD Person schema, favicon.
- `public/robots.txt`, `public/sitemap.xml` (static, listing routes with `BASE_URL` placeholder set to the deploy URL), `public/site.webmanifest`.
- Per-page `<title>`/`<meta>` via a tiny `useDocumentHead` hook (no react-helmet dep).
- Resume: `public/resume.pdf` placeholder + download link.

## 8. Accessibility & performance

Semantic HTML, ARIA on nav/menu/form, visible focus rings using `--ring`, proper heading hierarchy, `prefers-reduced-motion` respected, lazy-loaded route chunks via `React.lazy` for `/projects/:slug` and `/blog/:slug`, images `loading="lazy"`.

## 9. Documentation

`README.md` covering: what it is, local dev (`bun install`, `bun run dev`), build (`bun run build`), deploy (push to `main` → Action publishes to `gh-pages`; enable Pages in repo settings pointing to `gh-pages` branch), how to edit content (each `src/data/*.ts` file), how to swap the GitHub username. `LICENSE` (MIT).

## Out of scope (per "Core only")

Markdown engine, theme toggle (dark-only), command palette, PWA/offline, RSS, Mermaid, PDF.js viewer, syntax-highlighter lib, search/filter, keyboard shortcuts. Blog posts render as plain typographic HTML from the data file.

## Technical notes

- Vite `base: "/samuelgathemia/"` — all internal links use React Router (relative), assets use Vite's `import` so base is applied automatically.
- `public/404.html` copies `index.html` so deep links like `/projects/wifi-manager` work on GitHub Pages (standard SPA-on-Pages workaround, plus a small redirect script in `index.html`).
- No secrets, no backend, no Lovable Cloud.
