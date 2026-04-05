---
name: saving-to-marvin
author: "@ainbal"
description: Save analysis, insights, or any content to HeyMarvin as a file or Insight. Use this skill whenever the user wants to save something to Marvin, export findings to Marvin, create a Marvin note or insight, or says "save to Marvin" / "export to Marvin" / "save as insight". Also trigger for "refresh Marvin projects", "update my Marvin project list", or any request to persist content into Marvin. Even if the user just says "save that" or "put this in Marvin", use this skill — don't try to do it manually.
compatibility: Marvin MCP server (required — skill will not function without it)
---

# Save to Marvin

Help users save content from their Claude conversation into HeyMarvin by selecting a project and uploading the content as either:
- **A File** — for raw data, transcripts, documents (goes to Files tab)
- **An Insight** — for research reports, synthesized findings (goes to Insights & Playlists tab)

> **See also:** **querying-customer-research** skill (install separately under `.cursor/skills/querying-customer-research/`) — for searching and querying data already stored in Marvin.

---

## Prerequisites

- **Marvin MCP must be connected.** If it isn't available in your tool list, add it to your MCP config:

  **Cursor** (`~/.cursor/mcp.json`) or **Claude Code** (`~/.claude/mcp.json`):
  ```json
  {
    "mcpServers": {
      "marvin": {
        "command": "npx",
        "args": ["-y", "@heymarvin/mcp"]
      }
    }
  }
  ```
  Restart your editor after saving. For full setup docs, see [HeyMarvin MCP documentation](https://docs.heymarvin.com/mcp).

- **macOS note:** Some commands in Step 4 (`pbcopy`, `open`) are macOS-only. See cross-platform alternatives noted inline.

---

## Triggers

- "save to Marvin"
- "export to Marvin"
- "put this in Marvin"
- "save that"
- "save as Marvin insight"
- "create Marvin insight"
- "export as insight"
- "refresh Marvin projects"
- "update my Marvin project list"

---

## Workflow

### Step 1: Get the Project List

Call the Marvin MCP `list_projects` tool to fetch the user's projects.

Cache the results to `~/.marvin-projects.json` for faster future lookups:

```bash
# After fetching from MCP, save the project list:
echo '<projects_json>' > ~/.marvin-projects.json
```

Cache format:
```json
[
  {"id": 12345, "name": "Project Name"},
  {"id": 67890, "name": "Another Project"}
]
```

If the user says "refresh my Marvin projects" or "update project list", re-fetch from MCP and overwrite the cache, then confirm: "Updated! Found X projects."

### Step 2: Help the User Pick a Project

Ask the user how they'd like to find their project:
- **Search by name** — ask for a keyword, then filter in-context (see below)
- **Browse recent** — show the first 15 projects from the cache
- **Specific project ID** — use it directly

**Filtering projects:** Load the cached JSON and filter in-context rather than shelling out to Python. This avoids any risk of command injection from user-supplied search terms:

```bash
# Read the cached project list into context
cat ~/.marvin-projects.json
```

Then filter the results yourself based on the user's search term (case-insensitive substring match on `name`), and present up to 10 matches.

Present matches and confirm which project to use before proceeding.

### Step 3: Identify the Content to Save

Ask the user what they'd like to save:
- **Previous response** — the most recent analysis or output from the conversation
- **Specific text** — user provides or describes the content
- **File contents** — content from an uploaded or workspace file

Format the content as clean markdown:
- Use clear headings
- Include relevant context
- Keep it focused and actionable

Also ask for (or suggest) a **note title** based on the content.

### Step 4: Save as File and Open in Marvin

HeyMarvin accepts file uploads (not direct note/insight creation via API). Save the content as a `.txt` file and open the project for upload.

**Ask the user where they want to save:**
- **As a File** — goes into the Files tab (for raw data, transcripts, documents)
- **As an Insight** — goes into the Insights & Playlists tab (for research reports, synthesized findings)

```bash
# Sanitize title into filename
FILENAME=$(echo "NOTE_TITLE" | tr ' ' '-' | tr '[:upper:]' '[:lower:]' | tr -cd '[:alnum:]-').txt

# Write content to Downloads
cat << 'EOF' > ~/Downloads/$FILENAME
CONTENT_HERE
EOF

# Copy to clipboard as backup
# macOS:
cat ~/Downloads/$FILENAME | pbcopy 2>/dev/null || true
# Linux alternative: xclip -selection clipboard < ~/Downloads/$FILENAME
# Windows (WSL) alternative: clip.exe < ~/Downloads/$FILENAME

# Open the Downloads folder and the appropriate Marvin page
# macOS:
open ~/Downloads/

# For FILES (default):
open "https://app.heymarvin.com/projects/PROJECT_ID/files"

# For INSIGHTS:
open "https://app.heymarvin.com/projects/PROJECT_ID/insights"
# Linux alternative: xdg-open ~/Downloads/ && xdg-open "URL"
# Windows (WSL) alternative: explorer.exe ~/Downloads/ (then open the URL manually)
```

**Then tell the user:**

**If saving as a File:**
1. The file has been saved to their Downloads folder as `FILENAME`
2. The Marvin project Files page is now open in their browser
3. They should click **"Add new files"** and drag the file in

**If saving as an Insight:**
1. The file has been saved to their Downloads folder as `FILENAME`
2. The Marvin project Insights page is now open in their browser
3. They should click **"Add new Insight"** → **"Upload an existing document"** and select the file
4. After upload, they can edit the Insight to add clips, analysis, graphs, etc.

---

## Example Interaction

**User**: "Save that analysis to Marvin"

1. Fetch projects (or use cache)
2. Ask: "Which project? Search by name or browse recent ones."
3. User: "provider management"
4. Read `~/.marvin-projects.json` into context, filter for "provider management" → find "Provider Management" (ID: 35723), confirm with user
5. Ask: "What should I title this note?"
6. User: "Q1 User Research Findings"
7. Ask: "Save as a **File** (raw data) or as an **Insight** (research report)?"
8. User: "Insight"
9. Format the previous analysis as markdown
10. Save to `~/Downloads/q1-user-research-findings.txt`
11. Open Downloads + Marvin project's Insights page in browser
12. Tell user: "Done! Click 'Add new Insight' → 'Upload an existing document' and select `q1-user-research-findings.txt`"

---

## Tips

- Projects are sorted by most recently updated — browsing recent ones often works well
- Search is case-insensitive and partial-matches on project name
- Refresh the cache periodically so new projects appear
- Marvin accepts `.txt`, `.docx`, `.pdf`, and other formats
- Content is also copied to clipboard as a fallback in case drag-and-drop is inconvenient
- For EU accounts, replace `app.heymarvin.com` with `app.eu.heymarvin.com`

### Files vs. Insights — When to Use Each

| Save as... | Best for | What happens |
|------------|----------|--------------|
| **File** | Raw transcripts, documents, interview recordings, survey exports | Goes to Files tab; can be analyzed by Ask AI, tagged, annotated |
| **Insight** | Research reports, synthesized findings, stakeholder summaries | Goes to Insights tab; supports rich editing, clips, graphs, publishing, presentations |

**Pro tip:** After uploading as an Insight, you can enrich it in Marvin's editor with embedded clips, AI analysis, graphs, and callouts — making it more shareable with stakeholders.
