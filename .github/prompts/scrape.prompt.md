---
mode: agent
description: 'Search job portals for positions matching your profile, deduplicate across runs, and present matches sorted by fit.'
---

# /scrape - Job Portal Search

Execute the job-search workflow defined in [`skills/job-scraper/SKILL.md`](../../skills/job-scraper/SKILL.md).

Read that skill file in full and follow it exactly. In summary, you will:

1. Load the candidate profile and search queries from the profile files (`copilot.md`, `skills/job-application-assistant/`, and `search-queries.md`).
2. Search the Indian and international job boards listed in `search-queries.md` (Naukri, Instahyre, Wellfound, Indeed, LinkedIn, remote boards, etc.) via `WebSearch`/`WebFetch`. For LinkedIn, prefer the country-agnostic `linkedin-search` CLI in `.agents/skills/linkedin-search/`, passing `--location` for an Indian city, a target country, or `Remote`. (The Denmark-only CLIs — Jobbank, Jobdanmark, Jobindex, Jobnet — are not used for this market.)
3. Deduplicate results against previously seen jobs recorded under `job_scraper/`.
4. Score each new posting for fit against the profile and present the matches sorted by fit rating.
5. Offer to run `/apply <url>` on any match the user picks.

Persist newly seen jobs so subsequent runs only surface genuinely new postings.
