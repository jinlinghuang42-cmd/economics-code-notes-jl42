# Agent-assisted Research Workflow

This note records how AI agents can support empirical economics research, and how to actually
use and customize them.

**For me, agents are simply a faster, more accurate reference tool, playing the same role as
search engines, books, or journals. This note is not meant to replace my own thinking, but to
keep track of where they genuinely help.**

---

## Terms

- **Agent:** an AI assistant that acts inside a real workflow, not just chat. It reads files,
  runs code, and edits drafts. Software: Claude Code, Cursor, Codex, Gemini CLI.

- **Skill:** a folder `~/.claude/skills/<name>/` containing a `SKILL.md` file (open standard,
  [agentskills.io](https://agentskills.io)). The folder name becomes the command. A skill is
  **prompt-based**: when triggered, its instructions are loaded into the agent's context and the
  agent follows them. **It is written, not trained; no data-feeding or fine-tuning.** A `SKILL.md`
  has two parts:
  - **Frontmatter** (YAML between `---`): `name`, and `description`, which tells the agent when
    to invoke the skill automatically. Optional fields like `allowed-tools` restrict what it can do.
  - **Body** (Markdown): the instructions the agent executes. Can also take arguments after the
    command (e.g. `rewrite` in `/econ-write rewrite`) and inject live data such as a file or a
    command's output.

- **Mode:** one skill can serve several tasks (draft, rewrite, review), selected by the argument
  after the command, e.g. `/econ-write rewrite`. **A skill is triggered either by its slash command
  in the agent's chat box or just by asking in plain language**; which commands exist depends on the
  skill.

- **API** (Application Programming Interface): a defined way for programs to talk to each other.
  Instead of clicking through a website, code sends a request in a fixed format and gets data or a
  result back in a fixed format. "API" is not one thing but a type of interface. The ones common
  in empirical work:
  - **Data API:** pull structured data directly by code, e.g. FRED, World Bank, IMF, OECD,
    Census (cleaner and more reproducible than downloading by hand).
  - **Hidden / Web API:** many sites load their data from a backend endpoint returning JSON
    (visible in the browser's Network panel). Calling that endpoint is far easier and more stable
    than scraping the rendered HTML.
  - **LLM API:** call Claude / OpenAI from code (request: a prompt; response: the model's text) to
    label or classify text at scale, turning unstructured text into regression-ready variables.
  - Skill use does not need an API; APIs are for batch, programmatic tasks. They usually need a
    key (keep it out of GitHub) and have rate limits.

---

## References

**Method** 

- Korinek, A. (2023). "Generative AI for Economic Research." *Journal of Economic Literature*
  61(4): 1281–1317. [[AEA](https://www.aeaweb.org/articles?id=10.1257/jel.20231736) ·
  [site](https://www.korinek.com/research)] Regularly updated; latest "AI Agents for Economic
  Research," Aug 2025.


**Tools**

- [academic-research-skills](https://github.com/Imbad0202/academic-research-skills) — research-to-review pipeline.
- [econ-writing-skill](https://github.com/hanlulong/econ-writing-skill) — economics writing skill.
- [awesome-econ-ai-stuff](https://github.com/meleantonio/awesome-econ-ai-stuff) — catalog of skills by stage.
- [Coding for Economists](https://aeturrell.github.io/coding-for-economists/index.html) — Python.

---

## How agents help empirical research

Following Korinek (2023), LLM use in research falls into six areas:

1. **Ideation and feedback** — stress-test a research question.
2. **Writing** — draft and edit sections.
3. **Background research** — survey a literature (citations must be verified).
4. **Data analysis** — explore data, run and read analyses.
5. **Coding** — write, debug, translate code.
6. **Mathematical derivations** — set up and check derivations.

---

## Customizing in practice

Customizing means making the agent follow my conventions. Two paths: install an existing skill,
or build my own.

### Install an existing skill
In the terminal (commands change over time; check the skill's README):
```bash
curl -fsSL https://raw.githubusercontent.com/hanlulong/econ-writing-skill/main/install.sh | bash
```
Then invoke it inside the agent:
```text
/econ-write rewrite this paragraph for concision
```

### Build my own
1. Create the folder:
```bash
mkdir -p ~/.claude/skills/stata-do-template
```
2. Write `SKILL.md` inside it (structure: see Terms). Example, encoding my reproducible do-file
   conventions:
```markdown
---
name: stata-do-template
description: Scaffold a reproducible Stata do-file following my project conventions.
---

# Stata Do-file Template

When writing a Stata do-file, always:
- start with a header (project, purpose, author, date)
- setup: clear all / set more off / version 18 / set seed 12345
- define one `global root` and derive all paths from it; never hardcode absolute paths
- read only from `$raw`; write processed data to `$clean`; never overwrite raw
- export tables with esttab to `$tables`, figures with graph export to `$figs`
- split work into clean / analysis / tables do-files, called from a master.do
```
3. Restart the agent, then ask in plain language:
```text
write a do-file to clean my firm-year panel
```
The agent now scaffolds do-files my way every time. The same pattern turns any of my notes
(a clustering checklist, a merge-check routine) into a reusable skill.

### Triggering a skill
Two ways:
- **Slash command:** type `/<name>` (the folder name) in the agent's chat box. The surest way,
  and best for testing a skill just after building it.
- **Plain language:** describe the task and the agent reads each skill's `description` to decide
  whether to use it. Less reliable, so a clear `description` matters; if it does not fire, fall
  back to the slash command or name the skill explicitly.

---

## Need → Usage

By research stage. Add or drop entries as the workflow changes.

- **Literature review:** find and synthesize the closest papers.
  - *Use:* [`lit-review-assistant`](https://github.com/meleantonio/awesome-econ-ai-stuff/tree/main/_skills/literature/lit-review-assistant), or `deep-research` lit-review mode in [academic-research-skills](https://github.com/Imbad0202/academic-research-skills).
  - *Prompt:* "lit review on X, the 5 to 10 closest papers, in WHY/HOW/WHAT form."
  - *Manual check:* research scope; verify every citation exists and is faithful.

- **Data cleaning and sources:** turn raw files into an analysis dataset.
  - *Use:* [`stata-data-cleaning`](https://github.com/meleantonio/awesome-econ-ai-stuff/tree/main/_skills/data/stata-data-cleaning), [`api-data-fetcher`](https://github.com/meleantonio/awesome-econ-ai-stuff/tree/main/_skills/data/api-data-fetcher) (FRED, World Bank); [Coding for Economists](https://aeturrell.github.io/coding-for-economists/index.html) for Python.
  - *Prompt:* "Stata code to merge firm-year and industry-year, with merge checks."
  - *Manual check:* IDs and variables; follow my own do-file template.

- **Econometric analysis:** estimate and run diagnostics.
  - *Use:* [`stata-regression`](https://github.com/meleantonio/awesome-econ-ai-stuff/tree/main/_skills/analysis/stata-regression), [`r-econometrics`](https://github.com/meleantonio/awesome-econ-ai-stuff/tree/main/_skills/analysis/r-econometrics) (IV / DiD / RDD), [`python-panel-data`](https://github.com/meleantonio/awesome-econ-ai-stuff/tree/main/_skills/analysis/python-panel-data).
  - *Prompt:* "staggered DID with `csdid`, plus an event-study plot."
  - *Manual check:* the identification design; whether the assumptions hold; re-run all output.

- **Drafting a section:** get a structured first draft.
  - *Use:* [econ-writing-skill](https://github.com/hanlulong/econ-writing-skill) draft mode; or `academic-paper` in [academic-research-skills](https://github.com/Imbad0202/academic-research-skills).
  - *Prompt:* `/econ-write draft introduction` (Head's formula).

- **Polishing prose:** tighten and de-AI the writing.
  - *Use:* [econ-writing-skill](https://github.com/hanlulong/econ-writing-skill) rewrite mode (anti-AI patterns, McCloskey / Cochrane style).
  - *Prompt:* "rewrite for concision; cut passive voice and throat-clearing."
  - *Manual check:* the argument should stays mine.

- **Tables and figures:** publication-ready output.
  - *Use:* [`latex-tables`](https://github.com/meleantonio/awesome-econ-ai-stuff/tree/main/_skills/writing/latex-tables), [`econ-visualization`](https://github.com/meleantonio/awesome-econ-ai-stuff/tree/main/_skills/communication/econ-visualization); [econ-writing-skill](https://github.com/hanlulong/econ-writing-skill) latex-tips.
  - *Prompt:* "format this `esttab` output with booktabs and threeparttable."

- **Self-review:** audit before submission.
  - *Use:* `academic-paper-reviewer` (3 reviewers + score) in [academic-research-skills](https://github.com/Imbad0202/academic-research-skills); review-checklist in [econ-writing-skill](https://github.com/hanlulong/econ-writing-skill).
  - *Prompt:* "audit my paper from a methodologist, field expert, and writing critic."

- **Integrity check:** catch fabrication.
  - *Use:* integrity gate in [academic-research-skills](https://github.com/Imbad0202/academic-research-skills).
  - *Prompt:* "check every citation exists and actually supports the claim."
  - *Manual check:* re-verify references and re-run the numbers; never trust a reported result.

-- will add more as I read --
