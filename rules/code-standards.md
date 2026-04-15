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
// THROW. EXCEPTIONAL CASE
// REASON: `author/package` requires usage of throw statements to integrate `useLibrary`.
export function ThirdPartyIntegration() { ... }
```

# BANNED: useEffect

Direct usage of `useEffect` is BANNED in this codebase. Most `useEffect` usage compensates for something React already provides better primitives for. Banning the hook forces logic to be declarative, predictable, and event-driven, preventing race conditions, infinite loops, and dependency hell. Use a query library (e.g., React Query) for data fetching.

Banning `useEffect` forces cleaner tree design:
- Parents own orchestration and lifecycle boundaries.
- Children can assume preconditions are already met.
- Each unit does one job, and coordination happens at clear boundaries.

# `better-result`

`better-result` exposes several utilities to make TypeScript functional

- USE `tap()` and `tapAsync()` to run side effect on success values without changing the Result. Use for logging, metrics, or debugging
- USE Serialization API (`Result.serialize`). Results lose their methods when serialized with JSON.stringify. Useful for Storage, Server Actions, Message Queues, etc.

## Pipeable Composition

```typescript
import { pipe } from "better-result";

const processUser = pipe(
  fetchUser("123"),
  Result.map(user => user.name),
  Result.map(name => name.toUpperCase()),
  Result.match({
    ok: (name) => `User: ${name}`,
    err: (error) => `Failed: ${error.message}`,
  })
);
```

## Error handling

```typescript
// Base types
class ValidationError extends TaggedError('ValidationError')<{
  message: string;
  field: string;
}>() {}

class DbError extends TaggedError('DbError')<{
  message: string;
  operation: 'read' | 'write' | 'delete';
}>() {}

class AuthError extends TaggedError('AuthError')<{
  message: string;
  reason: 'expired' | 'invalid' | 'missing';
}>() {}

// Application-wide error union
type AppError = ValidationError | DbError | AuthError;

// Domain-specific subsets
type UserManagementError = ValidationError | DbError;
type AuthenticationError = AuthError | DbError;
```

## Pattern Matching

- ALWAYS include context in TaggedError. Store enough information to debug issues
- USE exhaustive matching. Prefer `matchError()` over if-else chains
- USE TaggedError for domain errors. `type DomainError = NotFoundError | PermissionError`

```typescript
type ApiError = NotFoundError | ValidationError | InternalError;

const toHttpResponse = (
  result: Result<Data, ApiError>
): HttpResponse => {
  return result.match({
    ok: (data) => ({
      status: 200,
      body: JSON.stringify(data),
    }),
    err: (error) => matchError(error, {
      NotFoundError: (e) => ({
        status: 404,
        body: JSON.stringify({ error: e.message }),
      }),
      ValidationError: (e) => ({
        status: 400,
        body: JSON.stringify({ field: e.field, error: e.message }),
      }),
      InternalError: (e) => ({
        status: 500,
        body: JSON.stringify({ error: "Internal server error" }),
      }),
    }),
  });
};
```

## Generators

To prevent untyped functions we prohibit the use of try-catch (throws)

- ALWAYS return `Result` from generator
- NEVER throw in generators
- USE `Result.await` in async generators

```typescript
// ❗ BANNED PATTERN
try {
  const user = await fetchUser(id);
  const validated = await validateUser(user);
  const saved = await saveUser(validated);
  return saved;
} catch (error) {
  // All errors lumped together
  // No type safety on error
  console.error(error);
  throw error;
}
```

```typescript
// ✅ APPROVED ERROR HANDLING PATTERN
const result = await Result.gen(async function* () {
  const user = yield* Result.await(fetchUser(id));
  const validated = yield* Result.await(validateUser(user));
  const saved = yield* Result.await(saveUser(validated));
  return Result.ok(saved);
});
// Result<User, NotFoundError | ValidationError | DatabaseError>

return result.match({
  ok: (user) => user,
  err: (error) => matchError(error, {
    NotFoundError: (e) => handleNotFound(e),
    ValidationError: (e) => handleValidation(e),
    DatabaseError: (e) => handleDatabase(e),
  }),
});
```

When any `yield*` encounters an `Err`, execution stops immediately:

```typescript
class ErrorA extends TaggedError("ErrorA")<{ message: string }>() {}
class ErrorB extends TaggedError("ErrorB")<{ message: string }>() {}

const result = Result.gen(function* () {
  const a = yield* Result.ok(1);                     // a = 1
  const b = yield* Result.err(new ErrorA({ message: "failed" })); // Stops here!
  const c = yield* Result.ok(3);                     // Never executed
  return Result.ok(a + b + c);
});
// Result<number, ErrorA> = Err(ErrorA)
```

## Retries

```typescript
class DeadlockError extends TaggedError('DeadlockError')<{
  message: string;
}>() {}

class TransactionError extends TaggedError('TransactionError')<{
  message: string;
  cause: unknown;
}>() {}

async function runTransaction<T>(
  operation: () => Promise<T>
): Promise<Result<T, DeadlockError | TransactionError>> {
  return await Result.tryPromise(
    {
      try: operation,
      catch: (e) => {
        const error = e as { code?: string; message: string };
        
        // PostgreSQL/MySQL deadlock codes
        if (error.code === '40P01' || error.code === 'ER_LOCK_DEADLOCK') {
          return new DeadlockError({ message: error.message });
        }
        
        return new TransactionError({ 
          message: error.message,
          cause: e 
        });
      }
    },
    {
      retry: {
        times: 3,
        delayMs: 50,
        backoff: 'exponential',
        shouldRetry: (e) => e._tag === 'DeadlockError'
      }
    }
  );
}
```
