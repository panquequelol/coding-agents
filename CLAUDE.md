**In all interaction and commit messages, be extremely concise and sacrifice grammar for the sake of concision.**

**ALWAYS USE PARALLEL AGENTS AND TOOLS WHENEVER POSSIBLE. Specialized Subagents MUST be worked to the bone.**

## Decisin power

- Don't flatter me. Be charming and nice, but very honest. Tell me something I need to know even if I don't want to hear it
- I'll help you not make mistakes, and you'll help me
- You have full agency here. Push back when something seems wrong - don't just agree with mistakes
- Flag unclear but important points before they become problems. Be proactive in letting me know so we can talk about it and avoid the problem
- Call out potential misses
- If you don’t know something, say “I don’t know” instead of making things up
- Ask questions if something is not clear and you need to make a choice. Don't choose randomly if it's important for what we're doing
- When you show me a potential error or miss, start your response with ❗️ emoji

## Architecture

- USE `bun` as a package manager (e.g: `bun add ai`)

...

## Conventions

### Quality Standards

- Make minimal, surgical changes
- **Never compromise type safety**: No `any`, no non-null assertion operator (`!`), no type assertions (`as Type`)
- **Make illegal states unrepresentable**: Model domain with ADTs/discriminated unions; parse inputs at boundaries into typed structures; if state can't exist, code can't mishandle it
- **Abstractions**: Consciously constrained, pragmatically parameterised, doggedly documented
- **AVOID CODE COMMENTS**: Explicit and descriptive naming > code comments. Just by reading the CODE should be able to predict what it will do. If a function or implementation needs comments, it needs to be broken down.

### **ENTROPY REMINDER**

This codebase will outlive you. Every shortcut you take becomes
someone else's burden. Every hack compounds into technical debt
that slows the whole team down.

You are not just writing code. You are shaping the future of this
project. The patterns you establish will be copied. The corners
you cut will be cut again.

**Fight entropy. Leave the codebase better than you found it.**

### TypeScript Patterns

TypeScript is infested by landmines, from `Promise` to try-catch blocks. Build non-ambiguous, highly deterministic systems. All steps in problem-solving must be explicit, pure and composable.

- Bias for functional programming.
- Errors as values. Be explicit if it fails and how.
- Larger files > many small components, code that isn’t used elsewhere is defined in the same file.
- Colocate code that changes often close together, code that changes together belongs together.
- Compose a program via multiple isolated functions, features are about piping data into the right shape.
- `Type` > `interface`
- `Function` > Arrow functions (`() => {...}`). Arrow functions only used in callbacks. Composing a feature? `Function`, Piping a map? `.map((...) => {...})`

## Subagents

Proactively invoke specialized subagents when their use fits your needs. Pair when you can take advantage of the combinations.

### Oracle

Use for: strategic second opinion on subtle regressions, better-solution assessment, complex multi-file debugging, and large refactor trade-offs.

Not for: routine diff review, implementation readiness checks, or narrow well-scoped tasks.

Pair with: Overseer for high-stakes review. Effect Sage for deep Effect architecture/debugging.

### Overseer

Use for: tactical review of changed files and diffs before commit or handoff. Focus on bugs, security issues, behavioral regressions, and implementation risks.

Not for: architecture strategy, broad refactor planning, or deep system-wide reasoning.

Escalate to: Oracle when review depends on cross-system reasoning or subtle invariants. Effect Sage when the hard part is Effect-specific correctness.

### Librarian

Use for: cross-repository research, understanding third-party libraries, reading framework code, exploring remote repositories, and finding real-world usage patterns.

Not for: local code review, generic planning, or tasks answerable from the current codebase alone.

Pair with: Oracle for architecture choices involving external libraries. Effect Sage for Effect ecosystem docs and patterns.

### Effect Sage

Use for: Effect.ts implementation and review when the hard part is Effect itself: Schema modeling, typed errors vs defects, services/layers, retries, timeouts, concurrency, resources, and boundary design.

Not for: plain TypeScript with no meaningful Effect design choice.

Pair with: Overseer for Effect-heavy diff review. Oracle for difficult Effect architecture, debugging, or refactor trade-offs.
