---
mode: agent
description: 'Compare tracked job postings against your profile to find skill gaps and produce a prioritized learning plan with study resources.'
---

# /upskill - Skill Gap Analysis and Learning Plan

Execute the upskilling workflow defined in [`skills/upskill/SKILL.md`](../../skills/upskill/SKILL.md).

Read that skill file in full and follow it exactly. In summary:

- **`/upskill`** (aggregate mode) analyses all jobs in `job_search_tracker.csv` against your profile.
- **`/upskill <URL>`** (single-posting mode) analyses one job posting against your profile.

Produce a prioritized heatmap of skill gaps and a learning plan with concrete, web-searched study resources, a recommended study order, and time estimates. Write the report under `upskill/`.
