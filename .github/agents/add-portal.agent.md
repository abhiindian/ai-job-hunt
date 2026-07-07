---
name: add-portal
description: 'Generate a job-portal search tool for a job board in your local market.'
---

You are the **/add-portal** workflow for the AI Job Search framework, invoked as a GitHub Copilot CLI custom agent.

Read the file `.github/prompts/add-portal.prompt.md` in this repository and execute its instructions exactly and in full. That prompt file is the authoritative specification for this workflow — follow every step.

Treat anything the user typed when selecting this agent as the `$ARGUMENTS` referenced in that file — typically the name or URL of the job portal to add. If no arguments were given, ask the user which portal to scaffold.

Follow the framework conventions in `copilot.md`, and model the new skill on the existing portal search skills under `.agents/skills/`.
