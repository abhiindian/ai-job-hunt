# AI Job Search — Agent Instructions

This file is auto-loaded by the **GitHub Copilot CLI** (and other `AGENTS.md`-aware
agents) as always-on context for this repository. It makes the framework's
commands and skills usable from the terminal.

## Profile & conventions

The candidate profile, framework workflow, and verification checklist live in
[`copilot.md`](copilot.md). Read it before acting on any job-search task. It is the
single source of truth for who the candidate is and how documents must be produced.

## Commands → custom agents

**The Copilot CLI does not expose prompt files as `/`-slash commands** (see
github/copilot-cli#618, #1113). Instead, each workflow is registered as a **custom
agent** in [`.github/agents/`](.github/agents/), which the CLI *does* support.

Each `.github/agents/*.agent.md` is now **fully self-contained** — the complete
workflow lives directly in the agent file, which is the single source of truth. The
matching `.github/prompts/*.prompt.md` is a thin pointer back to the agent file, kept
only so VS Code Copilot Chat still resolves the `/`-command. (This is deliberately the
opposite of the old layout, where the agent was a shim that told the model to go
"read" the prompt file — a two-hop instruction that weak local models mangled into a
`read_agent` loop.)

**To run a workflow in the terminal:** launch `copilot`, then type `/agent` and pick
the workflow from the list (or pass `--agent <name>`). The agent executes its own body
directly — there is no second file to open first.

| Original command | Copilot CLI agent (`/agent` →) | What it does |
|---|---|---|
| `/setup`         | `setup` (alias: `profile-setup`) | Onboard your professional profile |
| `/apply`         | `apply`         | Evaluate fit, then draft + review a tailored CV and cover letter |
| `/scrape`        | `scrape`        | Search job portals, dedupe, rank by fit |
| `/upskill`       | `upskill`       | Skill-gap analysis + prioritized learning plan |
| `/expand`        | `expand`        | Enrich profile from documents and public presence |
| `/reset`         | `reset`         | Reset profile data / clear the documents folder (destructive) |
| `/add-portal`    | `add-portal`    | Scaffold a job-portal search skill for your market |
| `/add-template`  | `add-template`  | Register a custom LaTeX CV / cover letter template |

Anything typed when selecting an agent is passed through as that workflow's
`$ARGUMENTS` (e.g. a job URL for `apply`, or `--section search` for `setup`).

## Skills

The Copilot CLI **auto-discovers and triggers** skills (`SKILL.md` capability packs)
from the directories it scans — `.github/skills/`, `.agents/skills/`, and
`.claude/skills/`. **All framework skills now live under
[`.agents/skills/`](.agents/skills/)**, so they are picked up automatically with no
`copilot skill add` step. Confirm what's active with `copilot skill list`.

**Portal search skills** — `linkedin-search` (country-agnostic; covers the Indian and
international markets). It wraps a Bun-based CLI in its `cli/` folder — run `bun install`
there once before first use. It powers `scrape`, alongside web search across Naukri,
Instahyre, Wellfound, Indeed, and remote boards (see
`.agents/skills/job-scraper/search-queries.md`).

**Workflow skills:**

- [`.agents/skills/job-application-assistant/`](.agents/skills/job-application-assistant/SKILL.md) —
  evaluating postings, tailoring CVs, cover letters, interview prep. Used by `apply`.
- [`.agents/skills/job-scraper/`](.agents/skills/job-scraper/SKILL.md) — scraping/deduping job
  listings. Used by `scrape`.
- [`.agents/skills/upskill/`](.agents/skills/upskill/SKILL.md) — skill-gap analysis. Used by `upskill`.

> These are auto-discovered because they sit in a scanned directory (`.agents/skills/`).
> Do **not** move them back to a top-level `skills/` folder — the CLI does not scan there,
> which was the original cause of skills silently never triggering.

## Golden rules

- Never invent profile facts. Pull only from `copilot.md`, `.agents/skills/`, and the
  user's own `documents/`.
- Confirm before any destructive action (see the `reset` workflow).
- When a workflow's agent file and this summary disagree, the agent file wins.

## Running the agents reliably (troubleshooting)

Each `.github/agents/*.agent.md` is **self-contained**: the whole workflow is in the
agent body, so running it means just executing what you already have — there is no
second file to open first.

- **Never call `read_agent`.** `read_agent` reads the live output of an
  *already-running background agent*; called on a workflow name it returns
  `Agent not found` and a weaker model can loop on it forever. There is nothing to
  "read" — the workflow is the text in front of you. Just do the steps. (The earlier
  failure mode was an agent shim that told the model to go *read* the prompt file;
  that indirection is gone.)

- **Use a capable model, in the foreground.** These are multi-step, *interactive*
  workflows — `setup`, `apply`, and `reset` stop to ask you questions and wait for your
  answers. A small local model (e.g. a 3B-active MoE like `qwen3.6-35b-a3b`) may still
  struggle with a long multi-step workflow even without the indirection. Prefer a
  stronger model (`copilot --model <capable-model>`), and run the workflow
  interactively — **not** as a backgrounded task, since a background agent can't answer
  its own questions to you.
