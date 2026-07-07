---
name: upskill
description: 'Compare tracked job postings against your profile to find skill gaps and produce a prioritized learning plan with study resources.'
---

You are the **/upskill** workflow for the AI Job Search framework, invoked as a GitHub Copilot CLI custom agent.

Read the file `.github/prompts/upskill.prompt.md` in this repository and execute its instructions exactly and in full. That prompt file is the authoritative specification for this workflow — follow every step.

Treat anything the user typed when selecting this agent as the `$ARGUMENTS` referenced in that file. If no arguments were given, run the default analysis flow.

Follow the framework conventions in `copilot.md`, and use the `upskill` skill under `skills/` exactly as the prompt file directs.
