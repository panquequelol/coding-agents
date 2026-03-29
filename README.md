# Agentes para programar super mega opinionados.

## READ FIRST!

- uso [bun](https://bun.com/) enves de npm
- uso `opencode`.
- uso [openspec](https://openspec.dev/) enves de `plan mode`. crea planes completos que no dejan espacio de error para gentes. es un CLI, gratis. tiene 3 comandos utiles: explore (pinponear con el agente), propose (crear plan), apply (implementar).

## AGENTS.md

explicito en lo que quiere, opinionado, orquestra usando subagentes. bias towards functional programming

[ver aqui](./AGENTS.md)

## SUBAGENTES

igual que los tools para agentes de ia, cuando haces cosas muy especificas pierde todo el proposito dejar que un agente tome decisiones

- [oracle](./subagents/oracle.md). supervisa todo tipo de decisiones
- [overseer](./subagents/overseer.md). revisa cambios antes de commit
- [librarian](./subagents/librarian.md). investiga documencion
- [effect-sage](./subagents/effect-sage.md). toma decisiones sobre [effect.ts](https://effect.website/)

[ver aqui](./subagents/)

## MCPs

opensrc, grep_app, effect-docs, sequential-thinking, deepwiki

[ver aqui](./mcp.json)

## SETUP

[Minimax (10% OFF)](https://platform.minimax.io/subscribe/coding-plan?code=3tTlhn3hjj&source=link) te permiten usar `claude code` 10 veces mas barato, yo lo uso con `opencode`

para trabajar en multiples features al mismo tiempo sin conflictos

- si usas `claude code` mira [conductor](https://www.conductor.build/)
- si usas cualquier otro agente de codigo por CLI (como `opencode`), echale un ojo a [superset](https://github.com/superset-sh/superset)

`conductor.json` corre cada vez que creas un nuevo workspace

```json
{
  "scripts": {
    "setup": "./conductor-setup.sh"
  }
}
```

`conductor-setup.sh`

```sh
#!/bin/bash
****
# Install dependencies
bun install

# Copy .env file
cp $CONDUCTOR_ROOT_PATH/.env .env

echo "✓ conductor setup finished"
```


## OpenCode

el json se ve algo asi

probablemente elimine `effect sage` y `polecat` en algun momento

```json
{
  "agent": {
    "effect-sage": {
      "description": "Effect.ts specialist for implementation and review: Schema modeling, typed errors vs defects, services/layers, retries, timeouts, concurrency, and boundary design. Verify current APIs.",
      "mode": "subagent",
      "color": "accent",
      "prompt": "{file:...}"
    },
    "librarian": {
      "description": "Research specialist for third-party libraries, remote repositories, cross-repository bugs, docs, source spelunking, and usage patterns. Use when package behavior, external APIs, or open-source precedent is the main uncertainty. Invoke when investigating dependencies, external providers, etc.",
      "mode": "subagent",
      "color": "success",
      "model": "opencode-go/minimax-m2.7",
      "prompt": "{file:...}"
    },
    "polecat": {
      "description": "Coding implementation for atomic tasks. Execution-only. It's fast, but cannot reason, needs authoritative, bounded, deterministic, highly specific instructions to avoid failure. Prompt with non-ambiguous authoritative order (Strip all decision making).",
      "mode": "subagent",
      "color": "warning",
      "model": "opencode-go/minimax-m2.7",
      "prompt": "{file:...}"
    },
    "oracle": {
      "description": "Strategic second-opinion for complex reasoning, subtle regressions, alternative solutions, multi-file debugging, and large refactor analysis. Invoke before acting or planning. Prompt with precise problem, constraints, and files.",
      "mode": "subagent",
      "color": "error",
      "model": "openrouter/google/gemini-3.1-pro-preview",
      "reasoning": {
        "effort": "medium"
      },
      "prompt": "{file:...}"
    },
    "overseer": {
      "description": "Tactical reviewer for changed files and diffs. Use proactively before commit or handoff to find bugs, security issues, behavioral regressions, and implementation risks. Escalate only when review needs system-wide reasoning.",
      "mode": "subagent",
      "color": "accent",
      "model": "openrouter/google/gemini-3.1-pro-preview",
      "reasoning": {
        "effort": "medium"
      },
      "permission": {
        "write": "deny",
        "edit": "deny"
      },
      "prompt": "{file:...}"
    },
    "deep": {
      "description": "Autonomous primary agent engineered for deep reasoning, extensive codebase research, and solving far-reaching 'new field' problems. Optimized exclusively for mission-critical software development",
      "mode": "primary",
      "color": "accent",
      "prompt": "{file:...}"
    }
  }
}
```
