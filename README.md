<p align="center">
  <img src="copilot_logo.png" alt="AI Job Search Assistant — powered by GitHub Copilot" width="200">
</p>

# AI Job Search

An AI-powered job application framework built on [GitHub Copilot](https://github.com/features/copilot). Fork it, fill in your profile, and let Copilot evaluate job postings, tailor your CV, write cover letters, and prepare you for interviews — running against GitHub's hosted models **or a local LLM on your own machine** via [Ollama](https://ollama.com) or [LM Studio](https://lmstudio.ai).

> [!NOTE]
> This project is a fork and adaptation of the excellent **[MadsLorentzen/ai-job-search](https://github.com/MadsLorentzen/ai-job-search)** by [Mads Lorentzen](https://github.com/MadsLorentzen). The original was built for Claude Code; this fork ports the workflow to GitHub Copilot and adds a local-LLM path. Full credit for the original design, the drafter-reviewer application pipeline, and the job-portal skills goes to Mads. Please ⭐ the [upstream repository](https://github.com/MadsLorentzen/ai-job-search) and consider supporting his work below.

<p align="center">
  <a href="https://ko-fi.com/madslorentzen">
    <img src="https://storage.ko-fi.com/cdn/kofi3.png?v=6" alt="Buy Mads Lorentzen a coffee at ko-fi.com" height="40">
  </a>
</p>

## Contents

- [What this is](#what-this-is)
- [How it works with GitHub Copilot](#how-it-works-with-github-copilot)
- [Prerequisites](#prerequisites)
- [Local LLM setup (Ollama & LM Studio)](#local-llm-setup-ollama--lm-studio)
- [Quick start](#quick-start)
- [Other commands](#other-commands)
- [File structure](#file-structure)
- [How `/apply` works](#how-apply-works)
- [Customization](#customization)
- [Tips for better results](#tips-for-better-results)
- [Acknowledgements](#acknowledgements)
- [License](#license)

## What this is

A structured workflow that turns GitHub Copilot into a full-stack job application assistant. The core workflow (self-profiling, fit evaluation, and the drafter-reviewer application pipeline) is **language- and country-agnostic**. The job search targets the **Indian and international markets** (Naukri, Instahyre, Wellfound, Indeed, LinkedIn, and remote boards) via web search, plus the country-agnostic LinkedIn CLI, and the pattern is designed to be swapped for any local job boards.

```
/setup          /scrape              /apply <url>
  |                |                     |
  v                v                     v
Fill in        Search job           Evaluate fit
your profile   portals              Score & recommend
  |                |                     |
  v                v                     v
Profile        Present matches      Draft CV + Cover Letter
files ready    with fit ratings     (LaTeX, tailored)
                   |                     |
                   v                     v
               Pick a match         Reviewer agent critiques
               -> /apply            -> Revise -> Final output
```

The framework encodes career-guidance best practices, including structured evaluation criteria, forward-looking cover letter framing, and optional salary benchmarking.

## How it works with GitHub Copilot

This repo drives GitHub Copilot through native mechanisms, and works from **both** the VS Code chat UI and the terminal:

- **Custom instructions** — [`copilot.md`](copilot.md) holds your candidate profile and the workflow rules Copilot follows. It acts as the persistent context for every chat in this workspace. [`AGENTS.md`](AGENTS.md) does the same job for the Copilot CLI and other `AGENTS.md`-aware agents.
- **Prompt files (VS Code)** — the `/setup`, `/apply`, `/scrape`, and related commands live in [`.github/prompts/`](.github/prompts/) as `*.prompt.md` files. In **Copilot Chat** (VS Code, Agent mode) you invoke them by typing `/` followed by the file name, e.g. `/setup`.
- **Custom agents (CLI)** — the same workflows are registered in [`.github/agents/`](.github/agents/) so the [Copilot CLI](https://github.com/github/copilot-cli) can run them. The CLI does not expose prompt files as slash commands, so launch `copilot`, type `/agent`, and pick the workflow (or pass `--agent <name>`). Each agent reads its authoritative prompt file, so the prompt files stay the single source of truth.

You interact with everything from Copilot Chat inside VS Code (or the GitHub Copilot CLI). There is nothing to deploy — the "app" is the set of prompt files plus your profile.

## Prerequisites

- [Visual Studio Code](https://code.visualstudio.com/) (**1.99+**) with the [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) and [GitHub Copilot Chat](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat) extensions
- An active [GitHub Copilot subscription](https://github.com/features/copilot) with **Agent mode** enabled — required even when you run inference on a local model, because the extension itself is the harness that executes the tool calls
- Python 3.10+
- [Bun](https://bun.sh) (for the LinkedIn job-search CLI tool)
- LaTeX distribution with `lualatex` and `xelatex`: [TeX Live](https://tug.org/texlive/) or [MiKTeX](https://miktex.org/). The CV compiles with `lualatex` (pdflatex often fails on modern MiKTeX installs with `fontawesome5` font-expansion errors); the cover letter compiles with `xelatex` because `cover.cls` requires `fontspec`.
- **Optional — for local inference:** [Ollama](https://ollama.com) *or* [LM Studio](https://lmstudio.ai), plus enough RAM/VRAM to run a tool-calling-capable model (16 GB is a realistic floor; 32 GB+ recommended). See the next section.

## Local LLM setup (Ollama & LM Studio)

You can run this framework entirely against a **local model** — nothing leaves your machine at inference time, and there is no per-token cost. GitHub Copilot Chat's "bring your own model" support lets you register a local provider and pick it from the model dropdown; the framework's prompt files and skills work unchanged.

> [!IMPORTANT]
> **This workflow is agent-heavy.** `/apply`, `/scrape`, and friends depend on tool calling (file edits, terminal commands, web search, spawning a reviewer agent) and long-context reasoning. Small or non-tool-calling models will stall or produce broken output. Use a **strong, tool-calling-capable** model — e.g. `qwen2.5-coder:14b`/`32b`, `llama3.3:70b`, `qwen2.5:32b`, or a comparable instruct model. Smaller models (7B/8B) can handle `/setup` and light chat but struggle with the full `/apply` PDF-verification loop. Copilot's own hosted models remain the most reliable path for `/apply`; local models are great for the profiling, drafting, and iteration steps.

### Option A — Ollama (native in the Copilot model picker)

Ollama is supported directly by the VS Code Copilot Chat model picker.

1. **Install Ollama** and start it (it runs a local server at `http://localhost:11434`):
   ```bash
   # macOS (Homebrew) — or download from https://ollama.com
   brew install ollama
   ollama serve
   ```
2. **Pull a capable model:**
   ```bash
   ollama pull qwen2.5-coder:14b
   # heavier, better tool use if you have the VRAM:
   # ollama pull qwen2.5-coder:32b
   # ollama pull llama3.3:70b
   ```
3. **Register it in VS Code:** open the Copilot Chat view, click the **model picker** (the model-name dropdown), choose **Manage Models…**, select **Ollama**, and tick the model(s) you pulled. VS Code auto-detects the local Ollama server.
4. **Select the model** from the dropdown, switch the chat to **Agent mode**, and run the framework commands (`/setup`, `/apply`, …) exactly as documented below.

If VS Code doesn't find the server, confirm `ollama serve` is running and reachable at `http://localhost:11434` (curl `http://localhost:11434/api/tags`).

### Option B — LM Studio (OpenAI-compatible endpoint)

LM Studio exposes an **OpenAI-compatible** local server, which you register in Copilot as an "OpenAI Compatible" provider.

1. **Install [LM Studio](https://lmstudio.ai)**, open the **Search/Discover** tab, and download a tool-calling instruct model (e.g. *Qwen2.5-Coder-14B-Instruct*, *Llama-3.3-70B-Instruct*). Prefer GGUF builds sized to your hardware.
2. **Start the server:** go to the **Developer** (**Local Server**) tab, load the model, and click **Start Server**. Note the base URL — it defaults to `http://localhost:1234/v1` — and the exact **model id** shown in the loaded-model list.
3. **Register it in VS Code:** open the Copilot Chat model picker → **Manage Models…** → **OpenAI Compatible** (BYOK). Enter:
   - **Base URL:** `http://localhost:1234/v1`
   - **API key:** any non-empty string (LM Studio ignores it locally, e.g. `lm-studio`)
   - **Model id:** the id from step 2
4. **Select the model**, switch to **Agent mode**, and run the framework commands.

You can verify the endpoint independently:
```bash
curl http://localhost:1234/v1/models
```

### Using a local model from the Copilot CLI

The [Copilot CLI](https://github.com/github/copilot-cli) reads model configuration from your Copilot settings/environment. If your CLI version supports a custom or local model endpoint, point it at Ollama (`http://localhost:11434/v1`) or LM Studio (`http://localhost:1234/v1`) — both speak the OpenAI-compatible protocol — then run the workflows via `/agent` as described in [How it works](#how-it-works-with-github-copilot). Support for local endpoints varies by CLI release; if yours can't target a local server yet, use the VS Code path above.

### Practical notes

- **Context window:** `/apply` feeds a job posting, your profile, and LaTeX drafts into one conversation. Run local models with the largest context you can afford (Ollama: set `num_ctx`; LM Studio: raise the context length when loading). A cramped context is the most common cause of truncated CVs.
- **Privacy:** with a local model, profile and job data stay on your machine for inference. Note that `/scrape`, `/expand`, and `/upskill` still perform **web searches / fetches** through the Copilot extension to gather live job and course data — that traffic is inherent to those features regardless of which model you pick.
- **Cost & offline use:** local inference is free and works without a Copilot inference quota, but you still need the Copilot extension/subscription for the agent harness and for web-search grounding.

## Quick start

### 1. Fork and clone

```bash
gh repo fork MadsLorentzen/ai-job-search --clone
cd ai-job-search
```

> This forks the upstream project. If you are cloning this Copilot-adapted fork instead, point `gh repo fork` at its URL.

### 2. Install job search tools

```bash
cd .agents/skills/linkedin-search/cli && bun install && cd ../../../..
```

For `linkedin-search` the install is optional: it has zero runtime dependencies and runs with plain `bun`; `bun install` only pulls TypeScript dev types.

### 3. (Optional) Point Copilot at a local model

If you want local inference, set up Ollama or LM Studio now — see [Local LLM setup](#local-llm-setup-ollama--lm-studio) — and select the local model in the Copilot Chat model picker before continuing. Otherwise Copilot uses its hosted models by default.

### 4. Set up your profile

Open the folder in VS Code, open **Copilot Chat**, switch to **Agent mode**, and run:

```
/setup
```

`/setup` offers three paths: read your `documents/` folder if you have one populated (CV PDF, LinkedIn export, diplomas, reference letters, past applications), import a single CV pasted in chat, or walk through an interview. It auto-detects what you have and asks. Documents-folder mode is idempotent and safe to re-run as you add more material; see `documents/README.md` for the layout.

### 5. Search for jobs

```
/scrape
```

This searches multiple job portals for positions matching your profile, deduplicates results, and presents them sorted by fit. Pick a match to run `/apply` on it directly.

### 6. Apply to a job

```
/apply https://www.linkedin.com/jobs/view/1234567890
```

If the URL can't be fetched (some job portals block automated access), you can paste the job description directly instead:

```
/apply <paste the full job description here>
```

This runs the full workflow: evaluate fit, draft CV + cover letter, review with a second agent, revise, and present the final output.

## Other commands

`/setup`, `/scrape`, and `/apply` form the core workflow. Four more prompt files extend it once your profile is in place:

- **`/expand`** enriches your profile by scanning public sources you've already linked in it (GitHub repos, portfolio site, Kaggle, Google Scholar) and looking up syllabi for named courses and certifications. Discovered competencies are added to your profile with a source tag. Useful right after `/setup` to surface skills that documents alone don't make explicit.
- **`/upskill`** analyzes the gap between your profile and your tracked job postings (or a single posting via `/upskill <URL>`). Produces a prioritized heatmap of skill gaps and a learning plan with web-searched study resources and time estimates. Useful for career planning between applications.
- **`/add-template`** registers your own LaTeX CV or cover letter template in place of the stock ones. It captures the template's instructions (compile engine, fonts, style rules, page limit), runs a mandatory test compile, and wires the template into `/apply`. See [LaTeX templates](#latex-templates) below.
- **`/add-portal`** generates a job-portal search tool for a job board in your market. It investigates the portal (search URL pattern, result structure, access rules), scaffolds the CLI tool from the same structure as the shipped ones, and test-runs a live query before registering. See [Job search tools](#job-search-tools) below.

`/reset` is also available, see [Starting over](#starting-over) below.

> In the **Copilot CLI**, invoke any of these via `/agent` (e.g. `--agent apply`) rather than as `/`-slash commands. See [`AGENTS.md`](AGENTS.md).

## File structure

```
ai-job-search/
├── copilot.md                         # Copilot custom instructions: candidate profile + workflow rules
├── AGENTS.md                          # Always-on context for the Copilot CLI and AGENTS.md-aware agents
├── .github/
│   ├── prompts/                       # GitHub Copilot prompt files (the /commands, VS Code)
│   │   ├── setup.prompt.md            # /setup onboarding (documents folder, CV import, or interview)
│   │   ├── scrape.prompt.md           # /scrape job-portal search
│   │   ├── apply.prompt.md            # /apply workflow (drafter-reviewer)
│   │   ├── expand.prompt.md           # /expand competency enrichment from documents and online presence
│   │   ├── upskill.prompt.md          # /upskill skill gap analysis and learning plan
│   │   ├── add-template.prompt.md     # /add-template register custom LaTeX templates
│   │   ├── add-portal.prompt.md       # /add-portal generate a job-portal search tool for your market
│   │   └── reset.prompt.md            # /reset wipe profile data or documents folder
│   ├── agents/                        # Copilot CLI custom agents (one per workflow above)
│   └── FUNDING.yml                    # Sponsor link (upstream author)
├── skills/                            # Skill/content library referenced by the prompts
│   ├── job-application-assistant/     # Core application skill
│   │   ├── SKILL.md                   # Skill definition
│   │   ├── 01-candidate-profile.md    # Your education, experience, skills
│   │   ├── 02-behavioral-profile.md   # PI/DISC/personality assessment
│   │   ├── 03-writing-style.md        # Tone, structure, do's and don'ts
│   │   ├── 04-job-evaluation.md       # Scoring framework for job fit
│   │   ├── 05-cv-templates.md         # LaTeX CV structure + tailoring rules
│   │   ├── 06-cover-letter-templates.md # LaTeX cover letter templates
│   │   └── 07-interview-prep.md       # STAR examples + interview framework
│   ├── job-scraper/                   # Job search orchestration
│   └── upskill/                       # /upskill skill gap analysis and learning plan
├── .agents/skills/                    # Job portal CLI tools
│   └── linkedin-search/               # LinkedIn public job listings (country-agnostic; India + international)
├── cv/
│   └── main_example.tex               # moderncv LaTeX template
├── cover_letters/
│   ├── cover.cls                      # Custom cover letter LaTeX class
│   └── OpenFonts/                     # Lato + Raleway fonts
├── templates/                         # Custom templates registered via /add-template
│   └── README.md                      # Folder layout instructions
├── documents/                         # Career source materials for /setup Path A and /expand
│   ├── README.md                      # Folder layout instructions
│   ├── cv/                            # Master CV (PDF or .tex)
│   ├── linkedin/                      # LinkedIn profile export (PDF)
│   ├── diplomas/                      # Degree certificates and transcripts
│   ├── references/                    # Reference letters
│   └── applications/                  # Past application records (<company>_<role>/)
├── salary_lookup.py                   # Salary benchmarking tool (BYO data)
├── tools/
│   ├── convert_salary_excel.py        # Convert salary Excel to JSON
│   └── README_SALARY_TOOL.md          # Salary tool setup instructions
├── job_scraper/                       # Scraper state (seen jobs, results)
├── upskill/                           # /upskill report output (markdown reports per run)
├── job_search_tracker.csv             # Application tracking spreadsheet
└── SETUP.md                           # Detailed setup guide
```

## How `/apply` works

The `/apply` command runs a **drafter-reviewer workflow** with mandatory PDF compilation:

1. **Parse** the job posting (URL or text)
2. **Evaluate fit** against your profile (skills, experience, culture, location, career alignment)
3. **Draft** a tailored CV and cover letter in LaTeX
4. **Spawn a reviewer agent** that researches the company and critiques the drafts
5. **Revise** based on the reviewer's feedback
6. **Compile and inspect** both PDFs: lualatex for the CV, xelatex for the cover letter. Copilot reads the rendered pages and iterates on the LaTeX until the CV is exactly 2 pages with no orphaned entry titles, and the cover letter is exactly 1 page with the signature visible and fonts consistent.
7. **Present** the final output with a verification checklist

All claims in the CV and cover letter are verified against your actual profile. The system never fabricates skills or experience.

### What makes this workflow different

- **PDF verification loop.** Most LaTeX-resume templates produce "looks fine in the .tex" output that breaks in the PDF: job titles orphan to the next page, cover letters spill onto page 2, bullet fonts silently fall back to the body font. The `/apply` command compiles and visually inspects every PDF and applies targeted fixes (`\needspace`, `\enlargethispage`, font-matching wrappers for list items) until the layout is clean. This runs automatically on every application.
- **Relevance-weighted CV cutting.** When a CV overflows 2 pages, the workflow does not cut mechanically from the "oldest" section. It scores each candidate line by (a) relevance to the target posting, (b) uniqueness in the document, and (c) whether the cover letter depends on it, and cuts the lowest-total-score line first. An older-role bullet that hits posting keywords survives ahead of a recent-role bullet that does not.
- **Drafter-reviewer separation.** The drafter writes; a second Copilot agent, spawned with a fresh context, researches the company and critiques the drafts. The drafter then revises. This catches missed keywords, weak framing, and generic language that a single pass often leaves in.
- **Token-efficient reviewer dispatch.** The reviewer agent receives drafts inline rather than re-reading them, and the verification checklist runs once at the end of the workflow rather than being duplicated by both agents. Note: the compile-and-inspect step spends some of those savings on PDF rendering and layout iteration — the workflow trades some end-to-end token cost for a real reduction in broken PDFs reaching the user.

> [!TIP]
> Step 6's compile-and-inspect loop is the most demanding part for a **local model** — it requires reading rendered PDF pages and making precise LaTeX edits over several iterations. If a local model gets stuck here, switch the model dropdown to a Copilot hosted model for the `/apply` step, then switch back for profiling and drafting.

## Customization

### Which files to edit manually

If you prefer editing files directly instead of using `/setup`:

| File | What to change |
|------|---------------|
| `copilot.md` | Your full profile (name, education, experience, skills, goals) |
| `01-candidate-profile.md` | Structured version of your CV data |
| `02-behavioral-profile.md` | Your behavioral assessment or self-assessment |
| `04-job-evaluation.md` | Skill match areas, career goals, motivation filters |
| `05-cv-templates.md` | Profile statement templates for different role types |
| `07-interview-prep.md` | Your STAR examples from actual experience |
| `search-queries.md` | Job search queries for your skills and location |

### Updating your search queries

As your priorities evolve, you can reconfigure just the job search without re-running the full profile setup:

```
/setup --section search
```

This re-runs the search configuration interview: which roles to target, which skills to search for, which locations, and which portals. It also suggests role types you may not have considered based on your profile.

### LaTeX templates

The CV uses [moderncv](https://ctan.org/pkg/moderncv) (banking style). The cover letter uses a custom `cover.cls` with Lato/Raleway fonts.

To use your own template instead, run:

```
/add-template
```

Point it at your `.tex` file (plus any `.cls`/`.sty` files or bundled fonts). The command interviews you for the template's instructions — compile engine, fonts and where they live, style rules to preserve, hard page limit — stores everything under `templates/`, runs a mandatory test compile, and activates the template so `/apply` drafts from it. Templates are stored with `[PLACEHOLDER]` tokens instead of personal data, so they're safe to commit and share.

- `/add-template --list` shows registered templates
- `/add-template --use <name>` switches between them
- `/add-template --use default` reverts to the stock moderncv / cover.cls templates

If you prefer doing it by hand, the manual route still works: update the guidance in `05-cv-templates.md` and `06-cover-letter-templates.md`.

### Job search tools

Job search targets the **Indian and international markets** — the `/scrape` workflow searches Naukri, Instahyre, Wellfound, Indeed, LinkedIn, and remote boards via web search (see `skills/job-scraper/search-queries.md`), and the shipped **`linkedin-search`** CLI covers LinkedIn for any location. To add a dedicated CLI for another specific job board (any country), run:

```
/add-portal
```

Give it your local job board's URL. The command investigates the portal (search-URL pattern, result-page structure, robots.txt/access rules), scaffolds a CLI tool with the same structure, commands, and output contract as the shipped ones, and test-runs a live query before registering anything. Auth-walled portals are declined, and portals with restrictive terms get a prominent personal-use-only warning in the generated tool. The generated tool is market-specific and lives in your fork; the generator itself is the universal part.

For a **country-agnostic** starting point, the repo also includes **`linkedin-search`** — a job-search tool built on LinkedIn's public, unauthenticated `jobs-guest` endpoints. It is field-agnostic, has **zero runtime dependencies** (runs with just `bun`), and takes the search location as an explicit flag, so it works for any market out of the box (`-l "Berlin, Germany"`, `-l "Mumbai, Maharashtra, India"`, `-l "Remote"`, …). It is intended for **personal use only** — automated access is against LinkedIn's Terms of Service, so keep volume low. See `.agents/skills/linkedin-search/SKILL.md`.

### Salary benchmarking

The salary tool works with any salary data you provide (union statistics, Glassdoor exports, personal research, etc.). See `tools/README_SALARY_TOOL.md` for the expected format and setup. If you don't have salary data, the salary step is simply skipped.

### Starting over

To wipe your profile data and start fresh:

```
/reset profile    # clears skill files, preserves framework rules
/reset documents  # deletes files from documents/ folder
/reset all        # both
```

`/reset` shows exactly what will be deleted and requires you to type `RESET` to confirm. Nothing is deleted until you do.

## Tips for better results

### Profile depth matters

The single biggest factor in output quality is how much detail you put into your profile. A thin profile produces generic applications; a detailed one enables genuinely tailored results.

- **Role descriptions:** Don't just list job titles. Describe what you actually did in each position: specific projects, tools used, responsibilities, and measurable achievements. The more material you provide, the more precisely the system can reframe your experience for different roles.
- **Skills in context:** Instead of listing "Python" or "project management," describe how and where you applied them. "Built ML pipelines for customer churn prediction in Python using scikit-learn" gives the system far more to work with than "Python, machine learning."
- **All onboarding paths work:** Whether you point `/setup` at your `documents/` folder, paste a single CV, or walk through the interview, the principle is the same: richer input produces sharper output.

### Choosing between hosted and local models

- **Hosted Copilot models** — most reliable for the full `/apply` PDF loop and multi-step agent work. Use these when output quality matters most.
- **Local models (Ollama / LM Studio)** — free, private, and offline-friendly for `/setup`, `/expand`, drafting, and iteration. Give them the largest context window your hardware allows, and prefer a strong tool-calling model. You can switch models per step from the chat dropdown without restarting the workflow.

### Career path discovery

The framework supports two distinct modes of job searching:

- **Explicit targeting:** You know which roles or sectors you want. The system helps refine and prioritize based on fit.
- **Latent opportunity discovery:** By analyzing your full history (not just job titles, but the actual work you did), the system can surface career paths you haven't considered. Transferable skills that map to unexpected industries, patterns in what you enjoyed or excelled at, or emerging roles that combine your domain expertise with new technology.

To get the most from this, invest time during `/setup` in describing not just your experience, but what energized you, what drained you, and what you'd want more of. This context directly shapes how the system evaluates fit and which roles it surfaces during `/scrape`.

## Acknowledgements

- **[Mads Lorentzen](https://github.com/MadsLorentzen)** — creator of the original [ai-job-search](https://github.com/MadsLorentzen/ai-job-search) framework this project is forked from. The entire workflow design, the drafter-reviewer application pipeline, and the original job-portal skills originate from his work. [Support him on Ko-fi](https://ko-fi.com/madslorentzen).
- [Mikkel Krogholm](https://github.com/mikkelkrogsholm) ([skills repo](https://github.com/mikkelkrogsholm/skills)) for the job search CLI skills.
- Adapted for [GitHub Copilot](https://github.com/features/copilot) with hosted and local-model (Ollama / LM Studio) support; originally built with Claude Code by [Anthropic](https://anthropic.com).

## License

MIT — inherited from the [upstream project](https://github.com/MadsLorentzen/ai-job-search). See [LICENSE](LICENSE).
