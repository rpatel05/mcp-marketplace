# Mixpanel MCP Marketplace

**Reference library of workflow patterns and skills for using Mixpanel with AI coding assistants.**

This repository provides battle-tested analysis and instrumentation workflows for [Mixpanel](https://mixpanel.com) users working with AI coding assistants like **Claude Code**, **Cursor**, and **Claude**.

> **Note**: This is a reference/template library, not an installable plugin system. Skills must be manually copied to your local environment (see [Quick Start](#quick-start) below).

---

## What's Inside

| Plugin                            | Description                                                                                                                                                               |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [mixpanel](./plugins/mixpanel/) | Reusable analysis and instrumentation skills covering reports, boards, experiments, session replays, data governance, and analytics tracking workflows |

---

## Mixpanel Plugin

The mixpanel plugin turns your AI assistant into an expert product analyst and instrumentation partner. Skills are organized into seven areas:

### Core Analytics

| Skill               | What it does                                                                 |
| ------------------- | ---------------------------------------------------------------------------- |
| `create-report`      | Creates Mixpanel reports (Insights, Funnels, Flows, etc.) from natural language descriptions                  |
| `create-board`  | Builds boards from requirements, organizing reports into logical sections |
| `analyze-report`     | Deep-dives a report to explain trends, anomalies, and likely drivers          |
| `analyze-board` | Reviews a board end-to-end, surfacing key takeaways and areas of concern |

### Product Insights

| Skill                    | What it does                                                                                   |
| ------------------------ | ---------------------------------------------------------------------------------------------- |
| `analyze-experiment`     | Designs A/B tests, monitors running experiments, and interprets results                        |
| `monitor-experiments`    | Triages all active and recently completed experiments by importance                            |
| `analyze-feedback`       | Synthesizes customer feedback into themes — feature requests, bugs, pain points, praise        |
| `discover-opportunities` | Finds product opportunities by cross-referencing analytics, experiments, replays, and feedback |
| `compare-user-journeys`  | Compares two user groups side-by-side to surface behavioral differences                        |

### Session Replay & Debugging

| Skill                 | What it does                                                                                                  |
| --------------------- | ------------------------------------------------------------------------------------------------------------- |
| `debug-replay`        | Turns bug reports into numbered reproduction steps by extracting the interaction timeline from Session Replay |
| `replay-ux-audit`     | Watches multiple session replays for a flow and synthesizes a ranked friction map                             |

### Analytics Instrumentation

| Skill                           | What it does                                                                                                  |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `diff-intake`                   | Reads a PR or branch diff and outputs a structured `change_brief` YAML for downstream skills                  |
| `discover-event-surfaces`       | From a change brief, lists candidate analytics events for PM prioritization                                   |
| `discover-analytics-patterns`   | Maps how analytics is already implemented in the repo (SDK calls, naming, imports)                            |
| `instrument-events`             | From prioritized event candidates, builds a concrete instrumentation plan and JSON tracking plan              |
| `add-analytics-instrumentation` | End-to-end workflow — reads code, decides what to track, and produces a full instrumentation plan in one pass |

A typical flow: `diff-intake` → `discover-event-surfaces` → `instrument-events`, with `discover-analytics-patterns` ensuring new tracking matches existing conventions.

### Data Governance

| Skill          | What it does                                                                  |
| -------------- | ----------------------------------------------------------------------------- |
| `audit-lexicon`  | Reviews Lexicon for data quality issues, missing descriptions, and inconsistencies |
| `update-lexicon` | Bulk updates event/property descriptions and tags in Lexicon |

### Briefings

| Skill          | What it does                                                                  |
| -------------- | ----------------------------------------------------------------------------- |
| `daily-brief`  | Morning briefing of the most important changes across your Mixpanel instance |
| `weekly-brief` | Weekly recap of trends, wins, and risks to share with your team or leadership |

---

## Quick Start

### Prerequisites

1. **Install the Mixpanel MCP Server** (required for all skills to function):

**Claude Code:**
- Add to your MCP settings or use Claude Code's MCP configuration
- Complete the OAuth flow when prompted

**Cursor:**
- Go to Settings > Agent > Tools & MCP > Add MCP Server
- Use this config:

```json
"mixpanel": {
    "command": "npx",
    "args": [
        "-y",
        "@mixpanel/mcp-server"
    ]
}
```

- Complete the OAuth flow in the browser when prompted

2. **Verify MCP Connection**: Ensure Mixpanel MCP tools are available (e.g., `Get-Projects`, `Run-Query`)

### Installing Skills

**Option 1: Manual Installation (Claude Code)**

Copy individual skills to your local skills directory:

```bash
# Create skills directory if it doesn't exist
mkdir -p ~/.claude/skills

# Copy a skill (example: create-report)
cp -r plugins/mixpanel/skills/create-report ~/.claude/skills/

# Repeat for other skills you want to use
```

Skills will automatically load in Claude Code. Test with `/create-report` or `/daily-brief`.

**Option 2: Clone and Reference**

Clone this repository and reference skill workflows as needed:

```bash
git clone https://github.com/rpatel05/mcp-marketplace.git
cd mcp-marketplace/plugins/mixpanel/skills
```

Use the SKILL.md files as templates for your own implementations.

**Option 3: For Teams**

Fork this repository and customize skills for your team's specific workflows and naming conventions.

---

## Repository Structure

```text
.claude-plugin/
  marketplace.json            # Marketplace metadata
plugins/
  mixpanel/
    .claude-plugin/
      plugin.json             # Plugin manifest
    skills/
      add-analytics-instrumentation/
        SKILL.md              # Skill definition and workflow
      analyze-board/
        SKILL.md
      analyze-experiment/
        SKILL.md
      analyze-feedback/
        SKILL.md
      analyze-report/
        SKILL.md
      audit-lexicon/
        SKILL.md
      compare-user-journeys/
        SKILL.md
      create-board/
        SKILL.md
      create-report/
        SKILL.md
      daily-brief/
        SKILL.md
      debug-replay/
        SKILL.md
      diff-intake/
        SKILL.md
      discover-analytics-patterns/
        SKILL.md
      discover-event-surfaces/
        SKILL.md
      discover-opportunities/
        SKILL.md
      instrument-events/
        SKILL.md
      monitor-experiments/
        SKILL.md
      replay-ux-audit/
        SKILL.md
      update-lexicon/
        SKILL.md
      weekly-brief/
        SKILL.md
```

---

## How Skills Work

Each skill is a markdown file (`SKILL.md`) that provides:
- **Workflow instructions** for the AI assistant
- **When to use** the skill
- **Step-by-step execution** patterns
- **Example interactions** and outputs
- **Best practices** and common mistakes

When you invoke a skill (e.g., `/daily-brief`), Claude reads the SKILL.md file and follows the workflow to use Mixpanel MCP tools appropriately.

---

## Requirements

- **MCP-compatible client** – Claude Code, Cursor, or Claude
- **Mixpanel account** with API access
- **Mixpanel MCP Server** – `@mixpanel/mcp-server` (installed via npm/npx)
- **Node.js** – for running the MCP server

---

## Testing Skills

After installing a skill, test it with a simple invocation:

```
/daily-brief
/create-report show me DAU for the last 7 days
/analyze-board for board ID 12345
```

If a skill doesn't work:
1. Verify Mixpanel MCP server is connected (`/mcp` to check status)
2. Ensure OAuth authentication completed successfully
3. Check that the skill file exists in `~/.claude/skills/`

---

## Contributing

We welcome contributions! Whether it's a new skill, improvement, or bug fix:

1. Fork this repo
2. Create a new branch for your feature
3. Follow the existing patterns in `plugins/mixpanel/skills/` for structure
4. Submit a PR with a clear description of what the skill does and how to use it

### Creating a New Skill

1. Create a new directory under `plugins/mixpanel/skills/your-skill-name/`
2. Add a `SKILL.md` file with frontmatter:

```markdown
---
name: your-skill-name
description: Brief description of what the skill does
---

# Your Skill Name

## When to Use

...

## Instructions

...
```

3. Test the skill locally before submitting

---

## Resources

- [Mixpanel Documentation](https://docs.mixpanel.com)
- [Mixpanel MCP Server](https://github.com/mixpanel/mcp-server)
- [Model Context Protocol](https://modelcontextprotocol.io/)

---

## License

MIT

---

## Support

- **Issues**: [GitHub Issues](https://github.com/rpatel05/mcp-marketplace/issues)
- **Mixpanel**: [mixpanel.com](https://mixpanel.com)
