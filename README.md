# marvin-insight-validator

Cursor / Claude Code skill: validate product ideas against **Hey Marvin** customer research and produce a structured **Insight Brief** (verdict, themes, quotes, cross-functional implications).

## Install

1. Clone this repo (or copy `SKILL.md`).
2. Place the skill where your agent loads skills, for example:
   - **Cursor:** `~/.cursor/skills/marvin-insight-validator/SKILL.md` (path may vary; see [Cursor skills docs](https://cursor.com/docs))
   - **Claude Code:** `.claude/skills/marvin-insight-validator/SKILL.md` in your project or user config

3. Configure the **Marvin MCP** server and, if needed, the companion **querying-customer-research** skill for tool names and OAuth.

## Triggers

- “Validate an idea”, “Check Marvin”, “Run the Insight Validator”, “insight brief”, evidence-backed validation from Marvin

## Requirements

- Marvin MCP server (search / Ask AI against your research workspace)

## License

Use and adapt per your organization’s policies.
