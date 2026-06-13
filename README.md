# setup, chiquitito

## AGENTS.md

explicito en lo que quiere, opinionado, orquestra usando subagentes. bias towards functional programming

[ver aqui](./AGENTS.md)

## SUBAGENTES

igual que los tools para agentes de ia, cuando haces cosas muy especificas pierde todo el proposito dejar que un agente tome decisiones

[sentinel](./subagents/sentinel.md). usa modo strategy antes de implementar y modo review para aprobar cambios

```
Strategic second opinions and engineering implementation specialist. Invoke for tactical code reviews, implementation approval, planning and before acting. Runs on 2 modes: Strategy (before implementation), and Review (after implementation). Use proactively before commit, handoff or on plan mode. Prompt with precise problem, constraints, implementation request and files. Explicitly state 'mode:strategy ...' | 'mode:review ...'
```

[librarian](./subagents/librarian.md). investiga documencion

```
Research specialist for third-party libraries, remote repositories, cross-repository bugs, docs, source spelunking, and usage patterns. Invoke when investigating dependencies, external providers and the world. Use when package behavior, external APIs, or open-source precedent is the main uncertainty.
```

[ver aqui](./subagents/)

## MCPs

opensrc, grep_app, exa, sequential-thinking, deepwiki

[ver aqui](./mcp.json)
