---
name: sugarrush-standards
description: Use when starting a new project, app, website, prototype or product for Sugar Rush Development (Jeroen Wever), when choosing a stack, framework, CSS approach, hosting, database or CI/CD, when setting up a repo, its docs or deployment, or when bringing an existing Sugar Rush project up to standard.
---

# Sugar Rush standards

Jeroen's defaults for every project. They win over generic "minimal" or "no framework" instincts. Deviate only when he says so, and record the deviation in the project's `AGENTS.md`.

## At kickoff, settle these before any code

Ask (or confirm from the request), one at a time:
1. **Public pages?** Do they need SEO, and in which languages?
2. **Accounts?** Login, roles, payments?
3. **Data?** What is stored, and is it relational?
4. **Address?** Domain or subdomain, and is email sent from it?

Then pick from the table below and state each choice in one line, so he can overrule it.

## Stack

| Area | Standard |
| --- | --- |
| Runtimes | `mise.toml` in the repo pins everything (bun, node, python via uv). Never system or brew runtimes. |
| JS | Bun for install, run, test and build. TypeScript, strict. |
| Public site (SEO, i18n) | Astro, static, with built-in i18n routing (`hreflang`, canonical, sitemap per locale). Interactive parts as Vue islands. |
| App behind login | Vue (try Vapor mode, fall back to regular Vue). Either Vue pages as client-only islands in the same Astro project, or a Vite SPA. `noindex`. |
| SSR needed | Nuxt, only when server rendering is a real requirement. |
| CSS | Modern vanilla CSS: custom properties, native nesting. In Vue SFCs `<style module="styles">` with `:class="styles.x"`. Never Tailwind. |
| Accessibility | WCAG 2.2 AA from the first commit, checked with axe in tests. Keyboard-only usable, visible focus, reduced motion. |
| Theme | `prefers-color-scheme` by default, plus a toggle that overrides it and is remembered. |
| Hosting | Cloudflare Workers with static assets (not Pages). Workflows for long jobs. Containers only for native binaries (ffmpeg and the like). |
| Data | D1 by default. Postgres (Neon) only when asked; then Hyperdrive with caching **off** and Neon's **direct** connection string, never its pooler. Files in R2, served through the Worker. |
| Email | Cloudflare Email Sending on a sending subdomain (e.g. `mail.<domain>`). Never change the apex SPF/MX when the domain already has mail (Google Workspace). |
| Payments | Stripe via plain `fetch`, signed webhooks, idempotent event handling. |

## Repo

- Under the GitHub org `sugarrushdev`, private by default.
- Work goes to `main` through pull requests, never direct pushes. On GitHub's free plan, private repos
  can't enforce this (branch protection and rulesets need GitHub Team), so it is a working agreement until the
  org upgrades. Never claim `main` is protected without checking.
- `CLAUDE.md` contains one line: `@AGENTS.md`.
- `.dev.vars` and `.env` are gitignored. Add a `.dev.vars.example` with names only.

**`AGENTS.md` at the root** (and in any folder with its own purpose) has these sections:
- **Intent:** why it exists, for whom, and the non-negotiables (what would make it a failure even if it works).
- **How it works:** the main parts and how they connect.
- **Commands:** dev, test, build, deploy.
- **Rules:** this project's standards and its recorded deviations.

**`docs/`:**
- `specs/YYYY-MM-DD-<topic>-design.md`: one per design, written before building it.
- `architecture.md`: Mermaid diagrams of the system, one request or job end to end, and the deploy path. Where data lives. Key decisions with their reasons.
- `deploy.md`: first-time setup and every secret, by name.
- `todo.md`: open work, grouped.

## CI/CD

- GitHub Actions. Every pull request runs typecheck, tests and build. Merging to `main` runs migrations, then `wrangler deploy`, in a `production` environment.
- Pin actions to commit SHAs, and add Dependabot for `github-actions`.
- Secrets: runtime keys via `wrangler secret put`. CI holds only the Cloudflare deploy token, the account id (a variable) and a database URL if migrations need one.

## Secrets

Jeroen enters every secret himself, in his own terminal (`wrangler secret put`, `gh secret set`, editing `.dev.vars`). Never ask him to paste a key, token or connection string into the chat, and never echo one.

## Copy

Honest copy only: no invented testimonials, user counts or logos, and no privacy promise the code doesn't keep. Plain words, active voice.

## Agents and git

In multi-agent runs (workflows, gauntlets), subagents never run git. The lead commits once per piece that passes, on a branch, and opens a PR.

## Common mistakes

| Mistake | Instead |
| --- | --- |
| Plain HTML/JS "to stay minimal" | Astro + Vue islands, or Vue for the app |
| Workers Builds or Pages for deploys | GitHub Actions with the PR flow above |
| A Postgres pooler string in Hyperdrive | The direct string, caching off |
| Turning on email sending for an apex that has Google mail | A `mail.` subdomain |
| Pushing straight to `main` | A branch and a PR |
| "Paste your API key here" | "Run `wrangler secret put NAME` in your terminal" |
