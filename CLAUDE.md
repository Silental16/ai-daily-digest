# CLAUDE.md — operational rules for the AI Daily Digest routine

This repo is driven by a scheduled routine that writes a daily AI-news digest to
`digests/YYYY-MM-DD.md` (+ optional `-story.md`). GitHub Actions sends committed
`digests/*.md` files to Telegram. These rules apply to EVERY run automatically,
including scheduled cloud runs. They augment the routine's prompt — follow them.

## 1. Anti-repetition is the #1 priority (do this BEFORE writing)

The recurring failure of this digest is repeating news already covered on prior days.
Before writing today's digest:

1. Read the **last 7** `digests/YYYY-MM-DD.md` files (the dates immediately before the
   target date). Build a mental "ALREADY COVERED" list of every headline and ongoing saga.
2. When synthesizing, **drop anything already covered** — unless there is a genuinely NEW
   development. For a continuing story, report ONLY the new fact and label it as an update
   ("апдейт", "день N"); never re-summarize the whole story as if it were fresh.
3. **Reject recycled news.** Roundup articles ("15 biggest AI stories today") recycle
   week-old items. Only include an item if it actually happened / was published on the
   target day (or the day before at the earliest). If you cannot date it to that window,
   drop it.
4. If little is genuinely new after filtering, say so honestly — use `☀️ Тихий день в
   мире AI` rather than padding with recycled or repeated items. A short, all-new digest
   beats a long, repetitive one.

## 2. Split models: research on Sonnet 5, synthesis on Opus 4.8

Regardless of which model the main loop runs on, delegate work so the cheap parallel part
runs on Sonnet 5 and the editorial part runs on Opus 4.8:

- **Research → Sonnet 5 subagents.** Fan out the web searches across ~4 parallel
  subagents launched in a single message via the Agent tool with
  `subagent_type: "general-purpose"` and `model: "sonnet"`. Split by theme (core news &
  models; Claude/Anthropic/MCP; tools/agents/viral; indie stories). Each subagent must
  return a compact, structured findings list — for every item: `headline`, a 2-3 sentence
  summary, `source_url`, and the **verified publication/event date** (not the date it
  appeared in a roundup). Tell them to flag anything they cannot date as `UNVERIFIED`.
- **Synthesis → an Opus 4.8 subagent.** Pass the collected findings + the "ALREADY
  COVERED" list to one Agent-tool call with `model: "opus"` that does the dedup, editorial
  ranking, "инсайт дня", and writes the digest file(s). (If the main loop is already Opus,
  you may write directly instead of spawning a subagent.)

The main loop only orchestrates and commits — keep it light.

## 3. Output format is load-bearing

The Telegram converter parses `**bold**`, `[title](url)`, `↳` source lines, and emoji
section headers. Keep the exact format used by recent `digests/*.md` files. Every news
item needs a `↳` source URL; at least 5 links in "Стоит почитать"; prefer primary sources
(anthropic.com, techcrunch, official blogs, GitHub) over aggregator blogs.

Style: Russian prose, English tech terms, concise, opinionated, 600–1200 words, no fluff.

## 4. Commit scope

During the routine's commit step, `git add digests/` ONLY. Never commit `CLAUDE.md`,
`ROUTINE.md`, or other non-digest files in that step (committing a `digests/*.md` file is
what triggers the Telegram send).

See `ROUTINE.md` for the full routine prompt reference.
