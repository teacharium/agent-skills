# Teacharium agent skills

Let your AI agent build and revise [Teacharium](https://app.teacharium.io) lessons for you.

This repo is a plugin for Claude, Codex, GitHub Copilot, and Gemini CLI. It gives your agent two things:

- **The Teacharium MCP server** (`https://app.teacharium.io/api/mcp`), so the agent can read and change your lessons. You sign in with your Teacharium account the first time it connects. No API key needed.
- **Two skills** that teach the agent how to write good Teacharium lessons. They use the same instructions as the built-in Teacharium assistant.

| Skill | Use it for |
| ----- | ---------- |
| [`teacharium-lessons`](skills/teacharium-lessons/SKILL.md) | Lesson structure, authoring approach, the MCP workflow, tool cheat sheet, safety rules |
| [`teacharium-step-content`](skills/teacharium-step-content/SKILL.md) | Step content: the 12x12 grid, Text and MDX tags, expressions, variables, custom fields, actions |

- **In Claude Code, a step-builder subagent**, [`teacharium-step-builder`](agents/teacharium-step-builder.md). The agent plans the lesson, then hands each step to this subagent, which runs on Sonnet to build steps faster and more cheaply. Other agents ignore it and build steps themselves with the skills.

Full guide: [Bring your own agent](https://docs.teacharium.io/docs/share-your-work/bring-your-own-agent).

## Install

### Claude Code

```bash
claude plugin marketplace add teacharium/agent-skills
claude plugin install teacharium@teacharium
```

Or in one step from inside Claude Code (2.1.275 or later):

```text
/plugin install teacharium --marketplace teacharium/agent-skills
```

Then run `/mcp`, choose **teacharium**, and sign in.

To get updates automatically, open `/plugin`, go to **Marketplaces**, select **teacharium**, and turn on auto-update. Or update by hand with `claude plugin update teacharium@teacharium`.

### Claude (claude.ai, Claude Desktop, Cowork)

1. Go to **Customize** > **Plugins** > **Add** > **Add marketplace**.
2. Enter `teacharium/agent-skills`.
3. Install the **Teacharium** plugin.
4. On the plugin's **Connectors** tab, connect **Teacharium** and approve access.

Plugins you install on claude.ai also show up in Claude Code.

### Codex

```bash
codex plugin marketplace add teacharium/agent-skills
codex plugin add teacharium@teacharium
```

Or run `/plugins` inside Codex and install **Teacharium** from the list. Codex asks you to sign in to Teacharium when the plugin is installed, and checks for updates each time it starts.

If you only want the server, without the skills:

```bash
codex mcp add teacharium --url https://app.teacharium.io/api/mcp
```

### GitHub Copilot CLI

```bash
copilot plugin marketplace add teacharium/agent-skills
copilot plugin install teacharium@teacharium
```

### Gemini CLI

```bash
gemini extensions install https://github.com/teacharium/agent-skills --auto-update
```

Then run `/mcp auth teacharium` in Gemini CLI to sign in.

### Cursor, VS Code, and other agents

Install the skills with the [`skills`](https://github.com/vercel-labs/skills) CLI. It finds the agents on your computer and asks which ones to install to:

```bash
npx skills add teacharium/agent-skills -g
```

Leave out `-g` to install into the current project only. Update later with `npx skills update`.

Then add the MCP server.

**Cursor**: add this to `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project). Cursor asks you to log in the first time.

```json
{
  "mcpServers": {
    "teacharium": { "url": "https://app.teacharium.io/api/mcp" }
  }
}
```

**VS Code (GitHub Copilot)**: add this to `.vscode/mcp.json`, or run **MCP: Add Server** from the Command Palette.

```json
{
  "servers": {
    "teacharium": { "type": "http", "url": "https://app.teacharium.io/api/mcp" }
  }
}
```

**Other agents**: any MCP client that supports Streamable HTTP with OAuth 2.1 can connect to `https://app.teacharium.io/api/mcp`. Clients that only run local (stdio) servers can use `npx -y mcp-remote https://app.teacharium.io/api/mcp`.

To install the skills by hand, copy the folders in [`skills/`](skills) to wherever your agent reads skills from. Most agents read `~/.agents/skills/` (all projects) or `.agents/skills/` (one project).

### ChatGPT

ChatGPT doesn't use skills, but it can connect to the server. Add a custom MCP server (a plugin, formerly called a connector) with the URL `https://app.teacharium.io/api/mcp` and OAuth sign-in. See [the ChatGPT steps](https://docs.teacharium.io/docs/share-your-work/bring-your-own-agent#connect-your-agent).

## No skill support?

Agents without skills can get the same guidance from the server: the `get_authoring_guide` tool or the `teacharium://guide/authoring` resource. The Teacharium docs are also available as AI-readable markdown at [llms.txt](https://docs.teacharium.io/llms.txt) and [llms-full.txt](https://docs.teacharium.io/llms-full.txt).

## About this repo

The files in `skills/` and `agents/` are generated from the prompts in the Teacharium app, so the skills and the built-in assistant always give the same advice. Don't edit them here; changes made by hand are overwritten on the next release.

| File | Read by |
| ---- | ------- |
| `.claude-plugin/marketplace.json`, `.claude-plugin/plugin.json` | Claude Code, claude.ai, Codex, GitHub Copilot CLI |
| `.agents/plugins/marketplace.json` | Codex |
| `.mcp.json` | Claude, Codex, GitHub Copilot CLI |
| `gemini-extension.json` | Gemini CLI |
| `skills/` | Every agent above, and the `skills` CLI |

Check the manifests with `claude plugin validate .`.
