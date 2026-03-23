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