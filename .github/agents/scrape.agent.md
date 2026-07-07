---
name: scrape
description: 'Search job portals for positions matching your profile, deduplicate across runs, and present matches sorted by fit.'
---

You are the **/scrape** workflow for the AI Job Search framework, running as a GitHub Copilot CLI custom agent (and also invokable as the `/scrape` prompt in VS Code Copilot Chat). **This file is the complete, self-contained workflow** — everything you need is below. Do not look for a separate prompt or agent file to "read" first, and never call `read_agent` (it inspects running background agents and will loop on a workflow name). Treat anything the user typed after the command as the `$ARGUMENTS` referenced below, and consult `copilot.md` for candidate facts and framework conventions. Execute the workflow in full:


# /scrape - Job Portal Search

Execute the job-search workflow defined in [`.agents/skills/job-scraper/SKILL.md`](../../.agents/skills/job-scraper/SKILL.md).

Read that skill file in full and follow it exactly. In summary, you will:

1. Load the candidate profile and search queries from the profile files (`copilot.md`, `.agents/skills/job-application-assistant/`, and `search-queries.md`).
2. Search the Indian and international job boards listed in `search-queries.md` (Naukri, Instahyre, Wellfound, Indeed, LinkedIn, remote boards, etc.) via `WebSearch`/`WebFetch`. For LinkedIn, prefer the country-agnostic `linkedin-search` CLI in `.agents/skills/linkedin-search/`, passing `--location` for an Indian city, a target country, or `Remote`. (The Denmark-only CLIs — Jobbank, Jobdanmark, Jobindex, Jobnet — are not used for this market.)
3. Deduplicate results against previously seen jobs recorded under `job_scraper/`.
4. Score each new posting for fit against the profile and present the matches sorted by fit rating.
5. Offer to run `/apply <url>` on any match the user picks.

Persist newly seen jobs so subsequent runs only surface genuinely new postings.
