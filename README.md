# skills

Cross-agent skills installable with [`npx skills`](https://github.com/vercel-labs/skills).
Skills:

- **mand**: a conversation condenser.
- **gauntlet-loop**: turns a goal into a builder-versus-critic prompt that loops until it beats a real reference.
- **grill-me**: interviews you about a plan until it holds up.
- **sugarrush-standards**: Sugar Rush Development's defaults for new projects (stack, Cloudflare hosting, repo, docs, CI/CD, secrets).

## Install

```bash
# see what's in the repo
npx skills add sugarrushdev/skills --list

# install mand (interactive: pick your agent(s))
npx skills add sugarrushdev/skills --skill mand

# or target agents non-interactively
npx skills add sugarrushdev/skills --skill mand -a claude-code -a codex

# install everything, user-wide instead of per-project
npx skills add sugarrushdev/skills -g
```

Works with any Agent Skills-compatible agent — Claude Code, Codex, opencode, and
others. The CLI drops each skill into that agent's skills directory
(`.claude/skills/`, `.agents/skills/`, …).

Keep installed skills fresh with:

```bash
npx skills update
```