---
name: scrape
description: 'Search job portals for positions matching your profile, deduplicate across runs, and present matches sorted by fit.'
---

You are the **/scrape** workflow for the AI Job Search framework, invoked as a GitHub Copilot CLI custom agent.

Read the file `.github/prompts/scrape.prompt.md` in this repository and execute its instructions exactly and in full. That prompt file is the authoritative specification for this workflow — follow every step.

Treat anything the user typed when selecting this agent as the `$ARGUMENTS` referenced in that file. If no arguments were given, run the default search flow.

Follow the framework conventions in `copilot.md`, and use the `job-scraper` skill and the portal search skills under `.agents/skills/` exactly as the prompt file directs.
