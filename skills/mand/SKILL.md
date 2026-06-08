---
name: mand
description: >-
  Capture the important parts of a long or multi-threaded conversation into a
  compact, structured "keep-block" so the context window can be compacted
  efficiently without losing the thread. Offer this proactively when the session
  has grown long, has spanned several distinct topics or decisions, or has hit a
  natural breakpoint — even if the user hasn't asked. Also runs when the user
  types /mand [optional focus]. Reach for it whenever a conversation starts to
  sprawl and it's worth condensing before continuing.
argument-hint: "[optional: what to prioritise keeping]"
user-invocable: true
# Deliberately NOT `disable-model-invocation`: the whole point is that Claude may
# offer this on its own. Deliberately NOT `context: fork`: this must run inline so
# it can actually see the conversation it's condensing.
# This skill uses the built-in AskUserQuestion tool, available by default in
# interactive Claude Code. Do NOT add a restrictive `allowed-tools` list that
# omits AskUserQuestion, or the polls below silently fall back to plain text.
---

# /mand — condense the story

*mand* is Dutch for "basket": take the long, rambling story and drop it in the
basket. The goal is to distil what actually matters from this conversation into a
tight block the user can keep, so the context can then be compacted (or the
session restarted) without losing the plot.

Everything the user sees is in **English** — labels included — so it reads for
anyone.

## Step 1 — Offer (only when auto-triggered)

If Claude reached for this on its own, do NOT dump a summary unprompted — that
interrupts. Ask once with `AskUserQuestion`:

- **Question:** "This thread's gotten long. Condense it into a keep-block?"
- **Options:**
  - "Yes, condense it"  *(mark as Recommended)*
  - "Not yet"
  - "Let me say what to focus on"

If the user picks "Not yet", drop it and carry on. If they pick the focus option,
let their free-text answer steer Step 2.

When the user typed `/mand` themselves, skip this step and go straight to Step 2.
If `$ARGUMENTS` is present, treat it as what to prioritise keeping.

## Step 2 — Ask what to keep (the poll)

Scan the conversation and identify the distinct threads, decisions, and open
problems. Then use `AskUserQuestion` to let the user pick what the keep-block
should preserve — they know what's load-bearing better than you do.

- **Question:** "Which of these should the keep-block preserve?"
- **Format:** multi-select.
- **Options:** the most significant threads you found, phrased tightly
  (e.g. "iOS betting-slip focus bug", "A/B middleware design", "Vite 8 upgrade").

AskUserQuestion takes up to 4 options per question and adds an "Other" field
automatically, so surface the 4 weightiest threads as options and rely on "Other"
to catch anything you missed or that the user wants to add. If there are clearly
more than four live threads, ask a second `AskUserQuestion` (up to 4 questions per
call) rather than cramming.

If the conversation has only one obvious thread, skip the poll and just build the block.

## Step 3 — Produce the keep-block

Be ruthless: drop anything already resolved-and-irrelevant or trivially
reconstructible. Favour the user's own decisions and constraints over
re-explaining your own reasoning. Use exactly this shape:

**MAND — <one-line topic>**
- **Goal / context:** why we're here, 1–2 lines
- **Decisions:** what's already locked in
- **Current state:** what's done / where things stand
- **Open + next step:** unresolved threads and the next concrete action
- **Constraints / preferences:** must-keeps (versions, conventions, "minimal solutions", etc.)
- **Pointers:** files, commands, links worth re-loading

Keep it scannable — bullets, not prose. Omit any section that's empty.

## Step 4 — Hand off to compaction

A skill can't shrink the context window itself; it only produces this block. So
close by telling the user how to finish the job:

- Run `/compact` to compress the history — you can give it a focus, e.g.
  `/compact keep the points in the MAND block above`, or
- Start a fresh session and paste the MAND block in as the new starting point.

`/clear` or a new session is the cleanest reset; `/compact` keeps you in place.