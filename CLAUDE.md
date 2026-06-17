# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```sh
bun install        # install dependencies
bun dev            # dev server at localhost:4321
bun build          # production build to ./dist/
bun preview        # preview the production build
bun astro check    # TypeScript + Astro type checking
```

Node >=22.12.0 required. The project uses bun as the package manager.

## Architecture

AgentNexus is an **Astro 6** marketing site + chat demo for a multi-agent RAG copilot aimed at university academic regulations. TypeScript strict mode throughout.

### Pages & routing

- `src/pages/index.astro` — landing page; assembles section components sequentially
- `src/pages/roles.astro` — chat demo UI; self-contained page with all markup, styles, and script inline

### Component structure (`src/components/`)

The landing page is split into section components rendered in this order:

| Component | Section anchor | Purpose |
|---|---|---|
| `Nav.astro` | — | Fixed nav, scroll-blur effect |
| `Hero.astro` | — | Full-screen hero with video background |
| `Manifesto.astro` | `#reto` | Value proposition with before/after cards |
| `Modules.astro` | `#agentes` | 2×2 grid of the 4 AI agents |
| `Architecture.astro` | `#arquitectura` | SVG pipeline diagram |
| `Fuentes.astro` | `#fuentes` | Document sources section |
| `Rubrica.astro` | — | Evaluation rubric section |
| `Footer.astro` | — | Footer |
| `TransitionOverlay.astro` | — | Full-screen transition on `.cta-enter` click |

### Design system

All tokens are CSS custom properties in `src/styles/global.css`:
- **Coral scale**: `--coral-100` through `--coral-700`
- **Semantic**: `--ink`, `--paper`, `--body`, `--muted`, `--line`, `--line-strong`, `--cream`, `--cream-02`
- **Gradients**: `--grad-orange`, `--grad-warm-bg`
- **Status colors**: `--verified` (green), `--signal` (blue)
- **Layout**: `--max: 1240px`, `--gutter: 2rem`

### Typography

- **Azurio** — custom serif (self-hosted OTF in `public/assets/fonts/`), used for headings and wordmark
- **Replica** — custom sans (self-hosted TTF), used for body text
- **JetBrains Mono** — Google Font, used for monospace labels and badges
- **Source Serif 4** — Google Font, used for editorial/kicker text

### Key patterns

**Scroll reveal**: Add `.reveal` class to any element. `index.astro` registers an `IntersectionObserver` that adds `.revealed` once it enters the viewport. Styles for this are in a `<style is:global>` block on `index.astro`.

**CTA transition**: Any `<a class="cta-enter">` link triggers the `TransitionOverlay` — a full-screen dark wipe before navigating. New CTA links that navigate to `/roles` should get this class.

**Chat (roles page)**: The `/roles` page sends requests to OpenRouter (`https://openrouter.ai/api/v1/chat/completions`) using `mistralai/mistral-7b-instruct` with SSE streaming. The API key is read from the `PUBLIC_OPENROUTER_API_KEY` environment variable (prefix `PUBLIC_` makes it available client-side in Astro). Set it in a `.env` file or Netlify environment variables.

**Canvas animation**: The roles page renders an interactive dot-field on a `<canvas id="field">` using `requestAnimationFrame`; it reacts to mouse movement.
