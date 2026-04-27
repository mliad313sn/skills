---
name: skills-router
description: |
  Master router for an external library of 823 specialized skills hosted on GitHub.
  Use this skill BEFORE answering any non-trivial technical request: code, design,
  data, ops, content, document creation, testing, MCP, deployment, etc. It selects
  the most relevant SKILL.md from the library and loads it on demand from GitHub —
  the library lives outside Claude.ai's per-account skills quota.
  Triggers: any request that mentions a framework, language, file format,
  workflow, methodology, deployment target, or asks "how do I do X". When in
  doubt, consult this router first.
---

# Skills Router

You have on-demand access to a library of **823 reusable skills** hosted at:

- **Repo**: `https://github.com/mliad313sn/skills`
- **Branch**: `claude/install-marketplace-skills-SA8EJ`
- **Raw base URL**: `https://raw.githubusercontent.com/mliad313sn/skills/claude/install-marketplace-skills-SA8EJ/`

The library is too large for Claude.ai's per-account skills quota. This router loads the right SKILL.md just-in-time from GitHub.

## Workflow — follow this on every technical request

### Step 1 — Decide if the library is needed

Use the library when the request involves any of: a specific framework or language (React, Django, Postgres, Flutter…), a file format (`.docx`, `.pdf`, `.xlsx`, `.pptx`), a workflow (TDD, code review, debugging), a deployment target (Vercel, Railway, Docker), document/design creation, MCP server building, agent engineering, testing, security, devops.

If the request is purely conversational or trivial, skip the library.

### Step 2 — Discover the matching skill

Read the companion file **`CATALOG.md`** in this skill folder. It lists every skill grouped by category, in the format:

```
- `skill-name` → `path/to/SKILL.md` — short description
```

Pick the best 1–3 matches based on the user's intent (verbs, file extensions, frameworks, tools mentioned). If multiple match, prefer in this order: `anthropics/` (official) > `superpowers/` > `vercel/` > `davila7/` > `everything-cc/`.

For broader search, fetch the machine-readable index instead:

```
{raw_base}skills.json
```

### Step 3 — Load the chosen SKILL.md from GitHub

Build the URL: `{raw_base}{path}` where `{path}` is the value from the catalog (e.g. `anthropics/frontend-design/SKILL.md`).

Use whichever fetch path is available in the current conversation, in this order:

1. **GitHub connector** (if configured in claude.ai → Settings → Connectors):
   call `get_file_contents` with `owner=mliad313sn`, `repo=skills`, `ref=claude/install-marketplace-skills-SA8EJ`, `path=<path>`.
2. **Web fetch / Research mode**: fetch the raw URL directly.
3. **Web search**: search `site:raw.githubusercontent.com mliad313sn skills <skill-name>` and fetch the result.
4. **Last resort**: ask the user to paste the SKILL.md contents or to enable a connector.

### Step 4 — Apply the loaded skill

Treat the fetched SKILL.md as authoritative for the rest of the conversation. If it references supporting files (scripts, references, examples, templates), fetch each one with the same method, resolving paths relative to the skill's directory. For example, if the skill's path is `anthropics/frontend-design/SKILL.md` and it references `references/design-tokens.md`, fetch:

```
{raw_base}anthropics/frontend-design/references/design-tokens.md
```

Tell the user, briefly, which skill you loaded and from where. Then proceed with the task using the skill's instructions.

### Step 5 — Cache within the conversation

Once a skill is loaded, keep using it for the rest of the conversation without re-fetching. Only consult the router again if the user pivots to a different domain.

## Output expectations

- **Be transparent**: when you invoke a library skill, say so in one line: *"Loaded `frontend-design` from the skills library."*
- **Be selective**: don't load more than 1–2 skills per turn. Bigger problems decompose into smaller turns.
- **Don't fabricate**: if a fetch fails, surface the error and ask the user how to proceed (enable connector, paste content, or work without the skill).
- **Stay frugal**: only fetch supporting files the loaded SKILL.md actually instructs you to use.

## Library at a glance

| Source | Skills | Notes |
|---|---|---|
| `anthropics/` | 17 | Official Anthropic skills (docx, pdf, pptx, xlsx, frontend-design, mcp-builder, claude-api, …) |
| `superpowers/` | 14 | Development methodology (TDD, debugging, planning, code review, git worktrees) |
| `vercel/` | 7 | React, Next.js, Vercel deploy, view transitions |
| `davila7/` | 602 | Broad community catalog organized by category (database, productivity, devops, security, …) |
| `everything-cc/` | 183 | Specialized skills (agent ops, finance, kotlin, django, evals, …) |

For full discovery → read `CATALOG.md` (in this skill folder).
For programmatic lookup → fetch `skills.json` from the repo.
