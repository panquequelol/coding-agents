# Coding Agents

Agent instructions, specialist agent prompts, code rules, and MCP setup.

## Use

Copy the files you need into an agent project. The main agent reads `AGENTS.md`.

| File | Use |
| --- | --- |
| [AGENTS.md](./AGENTS.md) | Main agent rules for concise technical text, code, and agent work. |
| [rules/code-standards.md](./rules/code-standards.md) | Code organization, TypeScript, React, and error-handling rules. |
| [commands/pr-main.md](./commands/pr-main.md) | Command rules to create a draft PR for `main`. |
| [mcp.json](./mcp.json) | MCP server configuration. |

## Specialist agents

Use a specialist agent for its assigned task. Give each agent the problem, constraints, relevant files, and a required output.

| Agent | Use |
| --- | --- |
| [Oracle](./subagents/oracle.md) | Get a second opinion for plans, debugging, specifications, and refactor choices. |
| [Sentinel](./subagents/sentinel.md) | Review changes before handoff or commit. Do not treat work as complete without its approval. |
| [Librarian](./subagents/librarian.md) | Research current or source-sensitive questions on the web. |

## MCP servers

`mcp.json` configures these servers:

- `opensrc`
- `grep_app`
- `deepwiki`
- `sequential-thinking`
