# Marvin skills

Hey Marvin–related Cursor / Claude Code skills in this repo:

| Skill | Folder | Purpose |
|--------|--------|---------|
| **Marvin Insight Validator** | [`SKILL.md`](SKILL.md) (repo root) | Validate product ideas against customer research and produce a structured **Insight Brief**. |
| **Save to Marvin** | [`save-to-marvin/SKILL.md`](save-to-marvin/SKILL.md) | Save analysis or exports to HeyMarvin as a **File** or **Insight** (project pick + upload flow). |

## Install

1. Clone this repo (or copy the skill folder(s) you need).
2. Place each skill where your agent loads skills, for example:
   - **Cursor:** `~/.cursor/skills/<skill-name>/SKILL.md` (path may vary; see [Cursor skills docs](https://cursor.com/docs))
   - **Claude Code:** `.claude/skills/<skill-name>/SKILL.md` in your project or user config

   For **Save to Marvin**, use the folder name `save-to-marvin` and put `SKILL.md` inside it (so `~/.cursor/skills/save-to-marvin/SKILL.md`).

3. Configure the **Marvin MCP** server. Optionally install the **querying-customer-research** skill for search/query workflows and OAuth notes.

## Triggers (quick reference)

- **Insight Validator:** “Validate an idea”, “Check Marvin”, “Run the Insight Validator”, “insight brief”
- **Save to Marvin:** “save to Marvin”, “export to Marvin”, “save as insight”, “refresh Marvin projects”, etc.

## Requirements

- Marvin MCP server

## License

Use and adapt per your organization’s policies.
