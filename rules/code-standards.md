# TypeScript

TypeScript is infested by landmines, from `Promise` to try-catch blocks. 

All steps in problem-solving must be explicit, pure and composable.

## TypeScript Patterns

- Bias for functinal programming.
- Colocate code that changes often close together, code that changes together belongs together.
- Compose a program via multiple isolated functions, features are about piping data into the right shape.
- `Type` > `interface`
- `Function` > Arrow functions (`() => {...}`). Arrow functions only used in callbacks. Composing a feature? `Function`, Piping a map? `.map((...) => {...})`
- Prefer small, cohesive modules organized around one primary domain type or concept
- In TypeScript, when a module is centered on a primary type, prefer an OCaml-style namespaced module pattern: `export type X = ...` plus `export const X = { ... } as const` for constructors, parsers, combinators, and other domain operations
- Prefer attaching domain logic to the module for its primary type rather than scattering it across generic utility files
- When a module starts accumulating substantial logic for other types or domains, split those concerns into their own sibling modules
- Prefer specific domain modules over catch-all `utils` files

# Error Handling

- USE errors as values over throwing exceptions for expected failure paths
- USE `better-result` (`dmmulroy/better-result`) for fallible operations
- USE tagged/structured error types over untyped error strings
- Reserve thrown exceptions for truly exceptional, unrecoverable, or framework-boundary cases
- Propagate errors explicitly; do not swallow them or replace them with success-shaped fallbacks

## Error Message Design

- Write error messages to help the reader understand and recover: say what happened, why it happened if known, what the impact is, and what to do next
- Prefer specific, concrete wording over vague or generic messages
- If the cause is unknown, say that plainly; do not invent false precision
- State what is still true or preserved, especially whether data, prior work, or system state remain intact
- Include the most useful recovery action or next diagnostic step
- Match detail to audience: user-facing errors should be plain and actionable; internal errors should include precise operational context needed for debugging
- Internal errors should name the failing operation, relevant identifiers, expected vs actual state when useful, and the most likely remediation path

## Error Thrown Exceptions

- BANNED: `try`, `catch`, `throw`. They swallow errors instead of propagating them.
- RESERVE thrown exceptions for truly exceptional, unrecoverable, or framework-boundary cases.
- USE Result type, it makes error handling functional and composable.
- USE `TaggedError` and `MatchError` for discriminated error unions and exhaustive pattern matching.

Exceptional usage of throwing must be documented with a statement and reason.

```typescript
// THROWN. EXCEPTIONAL CASE
// REASON: `author/package` requires usage of throw statements to integrate `useLibrary`.
export function ThirdPartyIntegration() { ... }
```

# BANNED: useEffect

Direct usage of `useEffect` is BANNED in this codebase. Most `useEffect` usage compensates for something React already provides better primitives for. Banning the hook forces logic to be declarative, predictable, and event-driven, preventing race conditions, infinite loops, and dependency hell. Use a query library (e.g., React Query) for data fetching.

Banning `useEffect` forces cleaner tree design:
- Parents own orchestration and lifecycle boundaries.
- Children can assume preconditions are already met.
- Each unit does one job, and coordination happens at clear boundaries.
