# How to install the Skills Router on claude.ai

## What this does

You upload **one** skill to Claude.ai. That skill teaches Claude how to discover and load any of the 823 skills in this GitHub repo *on demand*. The library lives outside Claude.ai's per-account skill quota — only the router counts against it.

## Prerequisites

- A Claude plan that supports custom skills upload (Pro / Max / Team / Enterprise).
- One of the following so Claude can fetch from GitHub:
  - **GitHub connector** in Claude.ai (Settings → Connectors → GitHub) — most reliable. Works without making the repo public.
  - **Research / web fetch** enabled in conversation — works if the repo is public.
  - **Web search** as a fallback — works if the repo is public.

## Make the repo accessible

- If using the **GitHub connector**: keep the repo private; the connector handles auth.
- If using **web fetch / search**: the repo must be public on github.com. Verify the raw URL resolves:
  ```
  https://raw.githubusercontent.com/mliad313sn/skills/claude/install-marketplace-skills-SA8EJ/anthropics/frontend-design/SKILL.md
  ```

## Build the upload ZIP

From the repo root, run:

```bash
cd skills-router && zip -r ../skills-router.zip . && cd ..
```

`skills-router.zip` must contain `SKILL.md` at the ZIP root (not inside a `skills-router/` folder). The command above zips the *contents* of the folder, which is correct.

Verify:

```bash
unzip -l skills-router.zip | head
# should list SKILL.md, CATALOG.md, HOW-TO-UPLOAD.md at top level
```

## Upload to Claude.ai

1. Open **claude.ai → Settings → Capabilities → Skills**.
2. Click **Upload skill**, select `skills-router.zip`.
3. Confirm `skills-router` appears in the list.

## Connect GitHub (recommended path)

1. **claude.ai → Settings → Connectors → GitHub** → install / authorize.
2. Grant access to the `mliad313sn/skills` repo (or your fork).
3. In a new conversation, the GitHub connector tools appear automatically.

## Test it

Open a new conversation and ask something concrete:

> *"Build me a polished React landing page for a SaaS product."*

Claude should:
1. Recognize the request matches the library's `frontend-design` skill.
2. Read `CATALOG.md` from the router skill folder.
3. Fetch `anthropics/frontend-design/SKILL.md` from GitHub via the connector or raw URL.
4. State which skill it loaded.
5. Proceed using that skill's instructions.

If it doesn't, prompt explicitly: *"Use the skills-router to find the right skill."*

## Updating the library

When you add or update skills in the repo, regenerate the catalog and re-upload the router:

```bash
python3 scripts/build-annuaire.py     # rebuilds skills.json, INDEX.md, etc.
python3 scripts/build-catalog.py      # rebuilds skills-router/CATALOG.md
cd skills-router && zip -r ../skills-router.zip . && cd ..
# Re-upload skills-router.zip in claude.ai (overwrite the existing one)
```

## Troubleshooting

- **"I can't fetch URLs"** — Claude doesn't have web access in this conversation. Either enable the GitHub connector, turn on research mode, or paste the SKILL.md content manually.
- **"Wrong skill picked"** — re-prompt: *"Reconsult the catalog and try again — I want the Postgres-specific skill."*
- **Token budget** — `CATALOG.md` is ~170 KB; if context gets tight, ask Claude to fetch `skills.json` from the repo and search programmatically rather than re-reading the whole catalog.
