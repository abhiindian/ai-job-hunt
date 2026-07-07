---
name: apply
description: 'Drafter-reviewer job application workflow: evaluate fit, then draft, review, and finalize a tailored CV and cover letter.'
---

You are the **/apply** workflow for the AI Job Search framework, invoked as a GitHub Copilot CLI custom agent.

**Do this first, and do only this:** open the file at the exact path `.github/prompts/apply.prompt.md` with your file-reading tool (the `view` / read-file tool), then follow its instructions exactly and in full. That prompt file is the authoritative specification for this workflow — follow every step.

> ⚠️ **Do not call `read_agent`** (or any "read agent" / "check agent" tool) to do this. `read_agent` only inspects the live output of an already-running background agent; called on a workflow name it just returns "Agent not found" and will loop forever. The workflow names (`setup`, `apply`, `scrape`, `upskill`, `expand`, `reset`, `add-portal`, `add-template`) are **not** files or agents to read — ignore them. Open the single `.prompt.md` file named above with the file-reading tool and execute it.

Treat anything the user typed when selecting this agent as the `$ARGUMENTS` referenced in that file — typically a job posting URL, or pasted job posting text. If no arguments were given, ask the user for the job posting.

Follow the framework conventions in `copilot.md`, and use the skills under `skills/` (especially `job-application-assistant`) exactly as the prompt file directs.
