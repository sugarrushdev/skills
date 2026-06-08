# skills

Cross-agent skills installable with [`npx skills`](https://github.com/vercel-labs/skills).
First up: **mand**, a conversation condenser.

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

## What mand does

`/mand` turns a long, sprawling session into a tight, curated **keep-block** so
you can compact context without losing the thread — it offers itself proactively
and asks (via a quick poll) which threads to keep before condensing.

> Named after the *Meneer Mandje* bit from Maxim Hartman's *Omroep Maxim*:
> "Mandje van Tichelaar… dat kan korter… nóg korter… **Mand!**" — which is
> exactly what this does to a conversation.
