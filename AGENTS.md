# AI Job Search — Agent Instructions

This file is auto-loaded by the **GitHub Copilot CLI** (and other `AGENTS.md`-aware
agents) as always-on context for this repository. It makes the framework's
commands and skills usable from the terminal.

## Profile & conventions

The candidate profile, framework workflow, and verification checklist live in
[`copilot.md`](copilot.md). Read it before acting on any job-search task. It is the
single source of truth for who the candidate is and how documents must be produced.

## Commands → custom agents

The framework's workflows were originally authored as VS Code Copilot Chat prompt
files in [`.github/prompts/`](.github/prompts/). **The Copilot CLI does not expose
prompt files as `/`-slash commands** (see github/copilot-cli#618, #1113). Instead,
each workflow is registered as a **custom agent** in
[`.github/agents/`](.github/agents/), which the CLI *does* support.

**To run a workflow in the terminal:** launch `copilot`, then type `/agent` and pick
the workflow from the list (or pass `--agent <name>`). Each agent simply reads and
executes its authoritative prompt file, so the prompt files remain the single source
of truth.

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

The Copilot CLI **auto-discovers and triggers** skills (`SKILL.md` capability packs).
Confirm what's active with `copilot skill list`. All eight framework skills are
registered:

**Portal search skills** — auto-discovered as *Project* skills from
[`.agents/skills/`](.agents/skills/): `linkedin-search` (country-agnostic; covers the
Indian and international markets). It wraps a Bun-based CLI in its `cli/` folder — run
`bun install` there once before first use. It powers `scrape`, alongside web search
across Naukri, Instahyre, Wellfound, Indeed, and remote boards (see
`skills/job-scraper/search-queries.md`).

**Workflow skills** — the CLI only scans `.github/skills/`, `.agents/skills/`, and
`.claude/skills/`, so the three under [`skills/`](skills/) are registered as *Custom*
skills via `copilot skill add` (kept in place so the prompt files and VS Code still
resolve them by path):

- [`skills/job-application-assistant/`](skills/job-application-assistant/SKILL.md) —
  evaluating postings, tailoring CVs, cover letters, interview prep. Used by `apply`.
- [`skills/job-scraper/`](skills/job-scraper/SKILL.md) — scraping/deduping job
  listings. Used by `scrape`.
- [`skills/upskill/`](skills/upskill/SKILL.md) — skill-gap analysis. Used by `upskill`.

> Re-register the Custom skills on a fresh machine with:
> `copilot skill add skills/job-application-assistant && copilot skill add skills/job-scraper && copilot skill add skills/upskill`

## Golden rules

- Never invent profile facts. Pull only from `copilot.md`, `skills/`, and the user's
  own `documents/`.
- Confirm before any destructive action (see the `reset` workflow).
- When a workflow's prompt file and this summary disagree, the prompt file wins.

## Running the agents reliably (troubleshooting)

Each `.github/agents/*.agent.md` is a thin shim: its whole job is to **open the
matching `.github/prompts/*.prompt.md` file with the file-reading tool** (`view`) and
then execute it. Two things make that fail in practice:

- **Do not call `read_agent` on a workflow name.** `read_agent` reads the live output
  of an *already-running background agent*; it cannot open a workflow definition and
  returns `Agent not found`. A weaker model can mistake the shim's "read … agent"
  wording for this tool and loop on it (`read_agent("setup")`, `read_agent("upskill")`,
  …) without ever opening the prompt file. The shims now say this explicitly — if you
  are the agent, open the `.prompt.md` file with `view` and ignore `read_agent`.

- **Use a capable model, in the foreground.** These are multi-step, *interactive*
  workflows — `setup`, `apply`, and `reset` stop to ask you questions and wait for your
  answers. A small local model (e.g. a 3B-active MoE like `qwen3.6-35b-a3b`) often
  can't follow the single "open this file and run it" instruction and stalls in the
  `read_agent` loop above. Prefer a stronger model (`copilot --model <capable-model>`),
  and run the workflow interactively — **not** as a backgrounded task, since a
  background agent can't answer its own questions to you.
