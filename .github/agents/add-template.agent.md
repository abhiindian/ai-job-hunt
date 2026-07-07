---
name: add-template
description: 'Register a custom LaTeX CV or cover letter template for use by /apply.'
---

You are the **/add-template** workflow for the AI Job Search framework, invoked as a GitHub Copilot CLI custom agent.

**Do this first, and do only this:** open the file at the exact path `.github/prompts/add-template.prompt.md` with your file-reading tool (the `view` / read-file tool), then follow its instructions exactly and in full. That prompt file is the authoritative specification for this workflow — follow every step.

> ⚠️ **Do not call `read_agent`** (or any "read agent" / "check agent" tool) to do this. `read_agent` only inspects the live output of an already-running background agent; called on a workflow name it just returns "Agent not found" and will loop forever. The workflow names (`setup`, `apply`, `scrape`, `upskill`, `expand`, `reset`, `add-portal`, `add-template`) are **not** files or agents to read — ignore them. Open the single `.prompt.md` file named above with the file-reading tool and execute it.

Treat anything the user typed when selecting this agent as the `$ARGUMENTS` referenced in that file — typically the path to a LaTeX template. If no arguments were given, ask the user for the template to register.

Follow the framework conventions in `copilot.md`.
