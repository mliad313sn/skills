# Claude Skills Repository

A curated collection of **823 skills** harvested from popular Claude Code marketplaces ([claudeskills.club](https://claudeskills.club) & [claudemarketplaces.com](https://claudemarketplaces.com)).

Future Claude projects (web, Code, API) can reference this repo as a skills source — point Claude at a skill's directory and it can load the `SKILL.md` plus any supporting files (scripts, references, templates).

## Annuaire — How to find a skill

| File | Use case |
|---|---|
| [`skills.json`](skills.json) | Machine-readable index. Programmatic lookup, RAG, agent tools. |
| [`skills.csv`](skills.csv) | `grep` / spreadsheet-friendly. Quick keyword scan. |
| [`INDEX.md`](INDEX.md) | All 823 skills, sorted alphabetically, with paths. |
| [`INDEX_BY_SOURCE.md`](INDEX_BY_SOURCE.md) | Grouped by upstream marketplace. |
| [`INDEX_BY_CATEGORY.md`](INDEX_BY_CATEGORY.md) | Grouped by topic (database, productivity, devops, …). |

**Programmatic example** — find every skill matching "react":

```bash
jq '.[] | select(.description | test("react"; "i")) | {name, path}' skills.json
```

```bash
grep -i react skills.csv | cut -d, -f1,4
```

## Sources

| Folder | Origin | Skills |
|---|---|---|
| `anthropics/` | [anthropics/skills](https://github.com/anthropics/skills) (official) | 17 |
| `superpowers/` | [obra/superpowers](https://github.com/obra/superpowers) | 14 |
| `vercel/` | [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) | 7 |
| `davila7/` | [davila7/claude-code-templates](https://github.com/davila7/claude-code-templates) | 602 |
| `everything-cc/` | [affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) | 183 |

## Skill layout

Each skill directory contains a `SKILL.md` (with YAML frontmatter: `name`, `description`, optional `tags`, `model`) and any supporting assets the skill needs.

```
<source>/<skill-name>/SKILL.md                       # flat (anthropics, superpowers, vercel, everything-cc)
<source>/<category>/<skill-name>/SKILL.md            # nested (davila7)
```

## Using a skill

- **Claude Code project**: copy the skill folder into your project's `.claude/skills/` (or set `--global` for user scope) and reference it by name.
- **Claude on the web / API**: provide the SKILL.md path or contents in the system prompt or via tool/file access; Claude loads supporting files relative to the skill's directory.
- **Discovery**: load `skills.json`, filter by name/description/tag, then read the chosen `skill_md` path.

## Attribution

All skills retain their original licenses and authorship. See each source repository for license details. Anthropic-authored skills © Anthropic; community skills © their respective authors.
