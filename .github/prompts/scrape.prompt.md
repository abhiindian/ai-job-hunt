---
mode: agent
description: 'Search job portals for positions matching your profile, deduplicate across runs, and present matches sorted by fit.'
---

# /scrape - Job Portal Search

Execute the job-search workflow defined in [`skills/job-scraper/SKILL.md`](../../skills/job-scraper/SKILL.md).

Read that skill file in full and follow it exactly. In summary, you will:

1. Load the candidate profile and search queries from the profile files (`copilot.md`, `skills/job-application-assistant/`, and `search-queries.md`).
2. Query the available job-portal CLI tools in `.agents/skills/` (Jobbank, Jobdanmark, Jobindex, Jobnet, LinkedIn) with the configured queries and locations.
3. Deduplicate results against previously seen jobs recorded under `job_scraper/`.
4. Score each new posting for fit against the profile and present the matches sorted by fit rating.
5. Offer to run `/apply <url>` on any match the user picks.

Persist newly seen jobs so subsequent runs only surface genuinely new postings.
