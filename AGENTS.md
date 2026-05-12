# Knowledge Work Plugins

This repository is the open-source home of **Knowledge Work Plugins** — a collection of file-based plugins for [Claude Cowork](https://claude.com/product/cowork) and [Claude Code](https://claude.com/product/claude-code). Each plugin bundles domain-specific skills, slash commands, and MCP connector definitions that turn Claude into a specialist for a particular job function.

The project is maintained by Anthropic and accepts partner-built plugins under `partner-built/`.

## What This Repo Provides

- **16 first-party plugins** covering roles such as Engineering, Sales, Marketing, Product Management, Data, Finance, Legal, Customer Support, Design, HR, Operations, Enterprise Search, Productivity, Bio-Research, PDF Viewer, and Cowork Plugin Management.
- **5 partner-built plugins** under `partner-built/` (Apollo, Brand Voice, Common Room, Slack, Zoom).
- A **marketplace manifest** (`.claude-plugin/marketplace.json`) that indexes all plugins for discovery by Claude Code.

Every plugin is **file-based** — markdown and JSON only. There is no source code, no compilation, and no runtime infrastructure.

## Technology Stack

- **No programming language runtime required.** Plugins are interpreted directly by Claude Code / Claude Cowork.
- **File formats:** Markdown (`.md`) and JSON (`.json`).
- **No build system, package manager, or dependency tree.**
- **License:** Apache License 2.0 (see `LICENSE`).

## Repository Layout

```
.
├── .claude-plugin/marketplace.json   # Root marketplace manifest listing all plugins
├── README.md                         # Human-facing project overview
├── LICENSE                           # Apache 2.0
├── AGENTS.md                         # This file
│
├── <plugin-name>/                    # One directory per plugin
│   ├── .claude-plugin/
│   │   └── plugin.json               # Plugin manifest (name, version, description, author)
│   ├── .mcp.json                     # MCP server definitions (HTTP endpoints, OAuth)
│   ├── skills/                       # Domain knowledge Claude uses automatically
│   │   └── <skill-name>/
│   │       └── SKILL.md              # Skill definition with YAML frontmatter
│   ├── commands/                     # Optional: explicit slash commands
│   │   └── <command-name>.md         # Command definition with YAML frontmatter
│   ├── README.md                     # Plugin-specific user documentation
│   ├── CONNECTORS.md                 # Optional: connector category → tool mapping
│   └── LICENSE                       # Optional: plugin-specific license
│
└── partner-built/                    # Third-party plugins
    └── <partner-name>/
        └── <same structure as above>
```

### Plugin Manifest (`.claude-plugin/plugin.json`)

```json
{
  "name": "engineering",
  "version": "1.2.0",
  "description": "Streamline engineering workflows...",
  "author": { "name": "Anthropic" }
}
```

### MCP Configuration (`.mcp.json`)

Defines HTTP-based MCP servers that Claude can call. Two patterns are common:

1. **OAuth-enabled HTTP servers:**
   ```json
   {
     "mcpServers": {
       "slack": {
         "type": "http",
         "url": "https://mcp.slack.com/mcp",
         "oauth": {
           "clientId": "...",
           "callbackPort": 3118
         }
       }
     }
   }
   ```

2. **Plain HTTP servers:**
   ```json
   {
     "mcpServers": {
       "linear": {
         "type": "http",
         "url": "https://mcp.linear.app/mcp"
       }
     }
   }
   ```

Some entries may have an empty `url` (e.g., `google calendar`, `gmail`), indicating a placeholder for a server that the user must configure.

### Skills (`skills/<skill-name>/SKILL.md`)

Each skill is a single markdown file with YAML frontmatter:

```yaml
---
name: code-review
description: Review code changes for security, performance, and correctness...
argument-hint: "<PR URL, diff, or file path>"
---
```

The body of the file contains:
- A heading matching the skill/command name (e.g., `# /code-review`)
- A reference to `CONNECTORS.md` for tool placeholders
- Structured workflow instructions for Claude
- "Standalone vs. Supercharged" sections explaining what works without integrations vs. with MCP connectors
- Output templates (e.g., markdown tables for code review findings)
- Tips for the end user

Skills may reference other skills (e.g., "See the **product-brainstorming** skill for detailed guidance").

Skills may also contain supplementary files in subdirectories:
- `references/` — reference docs (e.g., SQL dialect guides)
- `scripts/` — helper scripts (e.g., Python packaging scripts in `data/skills/...`)

### Commands (`commands/<command-name>.md`)

Commands follow the same markdown + YAML frontmatter pattern as skills. They represent explicit slash workflows the user invokes (e.g., `/brainstorm`, `/standup`, `/review`). The frontmatter uses `description` and `argument-hint`.

### Connector Placeholders

Plugins are **tool-agnostic**. They use category placeholders such as:

- `~~chat` — Slack, Microsoft Teams
- `~~source control` — GitHub, GitLab, Bitbucket
- `~~project tracker` — Linear, Jira, Asana
- `~~monitoring` — Datadog, New Relic
- `~~knowledge base` — Notion, Confluence
- `~~CI/CD` — CircleCI, GitHub Actions

The `CONNECTORS.md` file in each plugin documents which placeholders are used and which MCP servers are pre-configured in `.mcp.json`.

## Build and Test Commands

There is **no build process** and **no test suite**. Because plugins are declarative markdown/JSON files, validation is manual and review-based.

To verify a plugin locally:

1. **Install it in Claude Code:**
   ```bash
   # Add the marketplace
   claude plugin marketplace add anthropics/knowledge-work-plugins

   # Install a specific plugin
   claude plugin install engineering@knowledge-work-plugins
   ```

2. **Or load it as a local plugin:**
   ```bash
   claude plugin load ./engineering
   ```

3. **Review the rendered skill/command** by invoking it in a Claude Code session.

## Code Style Guidelines

Since the project contains no executable code, "style" refers to consistent structure in markdown and JSON files.

### Markdown Files (Skills and Commands)

- **YAML frontmatter is required.** Include `description` and `argument-hint` for commands; include `name`, `description`, and `argument-hint` for skills.
- **Use H1 headings** that match the slash-command name (e.g., `# /brainstorm`).
- **Include a `CONNECTORS.md` reference** near the top of every skill/command:
  ```markdown
  > If you see unfamiliar placeholders or need to check which tools are connected, see [CONNECTORS.md](../../CONNECTORS.md).
  ```
- **Structure workflows as numbered steps** with clear stage names.
- **Use ASCII box diagrams** for "Standalone vs. Supercharged" summaries when helpful.
- **Provide output templates** (markdown tables, structured headers) so Claude produces consistent, scannable results.
- **End with a "Tips" section** giving users guidance on getting the best results.
- **Use imperative voice** in workflow steps (e.g., "Review the code", "Pull the PR diff").

### JSON Files

- Use 2-space indentation.
- Keep keys in a consistent order: `name`, `version`, `description`, `author` for plugin manifests.
- Use lowercase with spaces for MCP server keys (e.g., `"google calendar"`).

## Testing Instructions

There is no automated testing framework. Testing is done by:

1. **Static review:** Verify that all markdown links resolve, YAML frontmatter is well-formed, and JSON is valid.
2. **Functional review:** Load the plugin in Claude Code and exercise each skill/command to confirm Claude follows the intended workflow.
3. **Cross-plugin consistency:** When adding a new plugin, mirror the directory structure and file naming conventions of existing first-party plugins (e.g., `engineering/`, `sales/`).

## Development Conventions

### Adding a New First-Party Plugin

1. Create a new directory at the repository root.
2. Add `.claude-plugin/plugin.json` with a unique name and version.
3. Add `.mcp.json` with relevant MCP server definitions (or omit if the plugin is standalone-only).
4. Create `skills/<skill-name>/SKILL.md` for each automatic skill.
5. Optionally create `commands/<command-name>.md` for explicit slash commands.
6. Add `README.md` documenting the plugin's purpose, command/skill tables, and example workflows.
7. Add `CONNECTORS.md` if the plugin uses MCP connectors.
8. Register the plugin in `.claude-plugin/marketplace.json` at the root.

### Adding a Partner Plugin

Place it under `partner-built/<partner-name>/` and follow the same structure. Update `.claude-plugin/marketplace.json` with an `author` field.

### Modifying the Marketplace Manifest

When adding or removing plugins, update `.claude-plugin/marketplace.json`. Supported source types:

- `"source": "./<local-path>"` — for plugins in this repo.
- `"source": { "source": "url", "url": "<git-url>", "sha": "<commit-sha>" }` — for external repos.
- `"source": { "source": "git-subdir", "url": "<git-url>", "path": "<subpath>", "ref": "<branch>", "sha": "<commit-sha>" }` — for subdirectories in external repos.

## Security Considerations

- **No secrets in the repository.** OAuth `clientId` values in `.mcp.json` are public client identifiers (e.g., Slack's `clientId`), not secrets. Access tokens are never committed.
- **Empty URLs are placeholders.** Some `.mcp.json` entries have `"url": ""` to indicate a connector the user must configure locally. Do not fill these with hardcoded endpoints unless you are adding a new official MCP server.
- **Plugin content is user-facing.** Skills and commands are interpreted by Claude and shown to end users. Avoid including internal-only procedures or sensitive domain knowledge.
- **Markdown is rendered as-is.** Do not include HTML or executable code blocks that could be misinterpreted by a client.

## Deployment Process

There is no deployment pipeline. Changes are released by merging to the default branch (`main`). Claude Code users pick up the latest version by re-installing or updating plugins from the marketplace.

## Contributing

Plugins are just markdown files. Fork the repository, make changes, and submit a pull request. When contributing:

- Follow the existing directory structure and naming conventions.
- Ensure YAML frontmatter is present and well-formed.
- Update `README.md` and `CONNECTORS.md` if you add or remove skills, commands, or connectors.
- Update `.claude-plugin/marketplace.json` if you add a new plugin.
- Keep changes minimal and focused on a single plugin or cross-cutting concern.
