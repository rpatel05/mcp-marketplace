# Mixpanel MCP Skills Playground

**Workflow patterns and skills for using Mixpanel with AI coding assistants.**

This repository provides analysis and instrumentation skills for [Mixpanel](https://mixpanel.com) users working with AI coding assistants — **Claude Code**, **Cursor**, **Codex**, and other MCP-compatible clients.

---

## What's Inside

| Plugin                          | Description                                                                                       |
| ------------------------------- | ------------------------------------------------------------------------------------------------- |
| [mixpanel](./plugins/mixpanel/) | Analysis, instrumentation, and data governance skills covering reports, boards, experiments, replays, taxonomy, and more |

---

## Quick Start

### 1. Install the Mixpanel MCP Server

<details>
<summary><strong>Claude Code</strong></summary>

```bash
claude mcp add mixpanel -- npx -y @mixpanel/mcp-server
```

Complete the OAuth flow when prompted.

</details>

<details>
<summary><strong>Cursor</strong></summary>

Go to **Settings > Agent > Tools & MCP > Add MCP Server** and add:

```json
{
  "mixpanel": {
    "command": "npx",
    "args": ["-y", "@mixpanel/mcp-server"]
  }
}
```

Complete the OAuth flow in the browser when prompted.

</details>

<details>
<summary><strong>Codex (OpenAI)</strong></summary>

Add the MCP server to your `.agents/plugins/` configuration. See the [Codex docs](https://platform.openai.com/docs/codex) for MCP setup.

</details>

<details>
<summary><strong>Other MCP Clients</strong></summary>

Any client that supports the [Model Context Protocol](https://modelcontextprotocol.io/) can connect to `@mixpanel/mcp-server`. Refer to your client's MCP documentation.

</details>

### 2. Install Skills

**Claude Code** — copy skills to your local skills directory:

```bash
git clone https://github.com/rpatel05/mcp-skills-playground.git
cp -r mcp-skills-playground/plugins/mixpanel/skills/daily-brief ~/.claude/skills/
```

**Cursor** — reference skills from `.cursor-plugin/` or copy SKILL.md files into your workspace.

**For Teams** — fork this repo and customize skills for your team's workflows and naming conventions.

### 3. Verify

```
/daily-brief
/create-report show me DAU for the last 7 days
/taxonomy
```

If a skill doesn't work:
1. Verify Mixpanel MCP server is connected (`/mcp` to check status)
2. Ensure OAuth authentication completed successfully
3. Check that the skill file exists in your skills directory

---

## Mixpanel Plugin Skills

### Core Analytics

| Skill            | What it does                                                              |
| ---------------- | ------------------------------------------------------------------------- |
| `create-report`  | Creates Mixpanel reports (Insights, Funnels, Flows, etc.) from natural language |
| `create-board`   | Builds boards from requirements, organizing reports into sections         |
| `analyze-report` | Deep-dives a report to explain trends, anomalies, and drivers            |
| `analyze-board`  | Reviews a board end-to-end, surfacing takeaways and concerns             |

### Product Insights

| Skill                    | What it does                                                              |
| ------------------------ | ------------------------------------------------------------------------- |
| `analyze-experiment`     | Designs A/B tests, monitors experiments, and interprets results          |
| `monitor-experiments`    | Triages all active and recently completed experiments                    |
| `analyze-feedback`       | Synthesizes customer feedback into themes                                |
| `discover-opportunities` | Finds product opportunities across analytics, experiments, and replays   |
| `compare-user-journeys`  | Compares two user groups side-by-side                                    |

### Reliability & Error Diagnosis

| Skill                  | What it does                                                              |
| ---------------------- | ------------------------------------------------------------------------- |
| `diagnose-errors`      | Investigates errors across custom error events to triage what's broken    |
| `monitor-reliability`  | Proactive reliability health check with KPI trends and page health scores |

### Account Health (B2B)

| Skill                  | What it does                                                              |
| ---------------------- | ------------------------------------------------------------------------- |
| `analyze-account-health` | Analyzes B2B account usage, engagement, risk signals, and expansion opportunities using Group Analytics |

### Session Replay & Debugging

| Skill             | What it does                                                              |
| ----------------- | ------------------------------------------------------------------------- |
| `debug-replay`    | Extracts reproduction steps from Session Replay                          |
| `replay-ux-audit` | Synthesizes a ranked friction map across multiple replays                 |

### Analytics Instrumentation

| Skill                           | What it does                                                  |
| ------------------------------- | ------------------------------------------------------------- |
| `diff-intake`                   | Reads a PR/branch diff and outputs a structured change brief  |
| `discover-event-surfaces`       | Lists candidate analytics events for PM prioritization        |
| `discover-analytics-patterns`   | Maps existing tracking patterns in the repo                   |
| `instrument-events`             | Builds instrumentation plan and JSON tracking plan            |
| `add-analytics-instrumentation` | End-to-end instrumentation workflow in one pass               |

Typical flow: `diff-intake` → `discover-event-surfaces` → `instrument-events`

### Data Governance

| Skill            | What it does                                                  |
| ---------------- | ------------------------------------------------------------- |
| `audit-lexicon`  | Reviews Lexicon for data quality issues and inconsistencies   |
| `update-lexicon` | Bulk updates event/property descriptions and tags             |
| `taxonomy`       | Generates and enforces event/property naming conventions      |

### Briefings

| Skill          | What it does                                                  |
| -------------- | ------------------------------------------------------------- |
| `daily-brief`  | Morning briefing of key changes across your Mixpanel instance |
| `weekly-brief` | Weekly recap of trends, wins, and risks                       |

---

## Multi-Platform Support

This repository includes manifests for multiple AI coding platforms:

```text
.claude-plugin/
  marketplace.json          # Claude Code marketplace manifest
.cursor-plugin/
  marketplace.json          # Cursor marketplace manifest
.agents/
  plugins/
    marketplace.json        # Codex / OpenAI agents manifest
plugins/
  mixpanel/
    .claude-plugin/
      plugin.json           # Claude Code plugin manifest
    .cursor-plugin/
      plugin.json           # Cursor plugin manifest
    .codex-plugin/
      plugin.json           # Codex plugin manifest
    skills/
      analyze-account-health/
      analyze-board/
      analyze-experiment/
      analyze-feedback/
      analyze-report/
      add-analytics-instrumentation/
      audit-lexicon/
      compare-user-journeys/
      create-board/
      create-report/
      daily-brief/
      debug-replay/
      diagnose-errors/
      diff-intake/
      discover-analytics-patterns/
      discover-event-surfaces/
      discover-opportunities/
      instrument-events/
      monitor-experiments/
      monitor-reliability/
      replay-ux-audit/
      taxonomy/
      update-lexicon/
      weekly-brief/
```

---

## CI / CD

Two GitHub Actions workflows keep the repo consistent:

- **Auto Version Bump** — patches plugin versions across all platform manifests when a PR touching `plugins/` is merged
- **Manifest Sync Check** — blocks PRs if `.claude-plugin`, `.cursor-plugin`, or `.codex-plugin` manifests have mismatched name, description, or version fields

---

## Contributing

1. Fork this repo
2. Create a branch for your feature
3. Follow existing patterns in `plugins/mixpanel/skills/`
4. Submit a PR — the manifest sync check will validate your changes

### Creating a New Skill

1. Create `plugins/mixpanel/skills/your-skill-name/SKILL.md`
2. Include frontmatter:

```markdown
---
name: your-skill-name
description: Brief description of what the skill does
---
```

3. Test the skill locally before submitting

---

## Requirements

- **MCP-compatible client** — Claude Code, Cursor, Codex, or any MCP client
- **Mixpanel account** with API access
- **Mixpanel MCP Server** — `@mixpanel/mcp-server` (via npx)
- **Node.js** — for running the MCP server

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

- **Issues**: [GitHub Issues](https://github.com/rpatel05/mcp-skills-playground/issues)
- **Mixpanel**: [mixpanel.com](https://mixpanel.com)
