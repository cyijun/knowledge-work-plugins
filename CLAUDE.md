# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A more exhaustive contributor reference lives in `AGENTS.md` — consult it for full conventions on JSON style, security expectations, and partner-plugin onboarding. This file summarizes the high-leverage facts.

## What this repo is

A **plugin marketplace** for [Claude Cowork](https://claude.com/product/cowork) and [Claude Code](https://claude.com/product/claude-code). Every "plugin" is a directory of **markdown + JSON only** — no source code, no build step, no runtime. Claude itself interprets the files at session time.

First-party plugins sit at the repo root (one directory each: `engineering/`, `sales/`, `finance/`, `data/`, …). Third-party plugins live under `partner-built/`. The single source of truth that lists every plugin is `.claude-plugin/marketplace.json` — adding or removing a plugin **must** include an edit there or the marketplace won't see it.

## Build / test / lint

**There is none.** Don't look for a package.json, Makefile, or CI test runner — this repo has no executable code. "Testing" means:

1. Validate JSON parses and YAML frontmatter is well-formed (visual review or `jq`).
2. Install the plugin locally and exercise the skill/command interactively:
   ```bash
   claude plugin marketplace add anthropics/knowledge-work-plugins
   claude plugin install <plugin-name>@knowledge-work-plugins
   # or load directly from the working tree:
   claude plugin load ./<plugin-name>
   ```
3. Confirm Claude follows the workflow as written.

Because there are no automated checks, **cross-plugin consistency matters**. When adding files, mirror the structure of an existing first-party plugin (e.g., `engineering/`) — same directory names, same frontmatter keys, same "Standalone vs. Supercharged" framing in skills.

## Plugin anatomy

```
<plugin-name>/
├── .claude-plugin/plugin.json   # name, version, description, author
├── .mcp.json                    # MCP server endpoints (HTTP, often OAuth)
├── skills/<skill>/SKILL.md      # Auto-loaded domain knowledge
├── commands/<cmd>.md            # Optional: explicit /slash commands
├── CONNECTORS.md                # Connector category → tool mapping
├── README.md                    # User-facing docs
└── LICENSE
```

Most plugins ship only `skills/` — `commands/` exists for a few (`pdf-viewer`, `product-management`). Skills may include `references/` (reference docs) or `scripts/` (helpers like the Python packagers in `data/skills/`).

## Skill & command file conventions

Every `SKILL.md` and command file follows the same shape:

1. **YAML frontmatter** with `name`, `description`, `argument-hint`. The `description` is what triggers Claude to pull the skill in — it should describe both the scenario and the trigger phrases users would naturally say.
2. **H1 heading** matching the slash-command name (e.g., `# /code-review`).
3. **Pointer to `CONNECTORS.md`** for placeholder resolution.
4. **"Standalone vs. Supercharged" ASCII box** — what works without integrations vs. what unlocks when MCP connectors are present. This pattern is load-bearing; preserve it when editing or cloning.
5. **Numbered workflow steps** in imperative voice.
6. **Output templates** (markdown tables, fixed headers) so Claude produces scannable results.
7. **Tips section** at the end.

When a skill references tools, use **connector placeholders** (`~~chat`, `~~source control`, `~~project tracker`, `~~knowledge base`, `~~CI/CD`, `~~monitoring`, …) rather than naming specific products. `CONNECTORS.md` in each plugin documents the placeholder-to-tool mapping for that plugin's `.mcp.json`.

## MCP configuration

`.mcp.json` defines HTTP-based MCP servers Claude can call. Two patterns:

- **OAuth-enabled** entries include an `oauth` block with `clientId` and `callbackPort`. The `clientId` is a public identifier (e.g., Slack's), not a secret — committing it is intentional.
- **Plain HTTP** entries just have `type: "http"` and a `url`.

Some entries deliberately have `"url": ""` — these are **placeholders the end user fills in locally**. Do not invent endpoints to "fix" an empty URL. Server keys use lowercase with spaces (e.g., `"google calendar"`).

## When adding a new plugin

1. Create `<plugin-name>/` at the repo root (or under `partner-built/` for third-party).
2. Add `.claude-plugin/plugin.json` with a unique `name`, `version`, `description`, `author`.
3. Add `.mcp.json` if the plugin uses connectors (omit for standalone-only plugins like `cowork-plugin-management`).
4. Add `skills/` (and optionally `commands/`), each file with full frontmatter.
5. Write `README.md` and `CONNECTORS.md`.
6. **Register the plugin in `.claude-plugin/marketplace.json`** — this step is the most commonly missed.

Partner plugins additionally set the `author` field in the marketplace entry.

## Editing notes specific to this repo

- **Never commit `.env`-style secrets** — but also remember `clientId` values in `.mcp.json` are *not* secrets.
- **Don't introduce code, build tooling, or CI configuration** unless explicitly asked. The declarative-only nature is a feature.
- **Markdown is rendered as-is to end users.** Avoid HTML or executable code blocks that a client might misinterpret.
- **Skills cross-reference each other** by name (e.g., "See the **product-brainstorming** skill"). If you rename a skill, grep for inbound references.
