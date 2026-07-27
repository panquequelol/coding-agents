Run `thermo-nuclear-code-quality-review` and `deslop` on the current branch. Use Sentinel as the approval gate.

## Scope

Before cycle 1, record an immutable scope lock:

- task goal and non-goals
- `git merge-base main HEAD`
- task-owned files from the committed, staged, unstaged, and untracked changes
- changed behavior and direct callers, tests, and canonical helpers for changed contracts
- required checks, expected passing results, and allowed optional checks

Review only branch-caused defects, task requirements, and structural regressions. Report a finding only when evidence shows a correctness, safety, or maintainability risk and a minimal in-scope fix exists. Keep pre-existing cleanup and style-only notes out of the findings.

Build the target manifest from task-owned files. Add direct callers, tests, and canonical helpers only when they verify a changed contract. Exclude unrelated dirty files and record the exclusions.

Treat any change to `HEAD` or the worktree as a target change. Step 6 invalidates the snapshot and advances the cycle.

## Review bar

Use these checks as search prompts. Report only concrete, branch-caused risks:

- branches, special cases, flags, duplication, missed simplifications, and spaghetti conditionals
- wrong-layer logic, duplicate canonical helpers, hidden state, implicit resolution, and magic behavior
- needless wrappers, casts, `any`, `unknown`, optional contracts, and unsafe types
- sequential orchestration, partial updates, deep nesting, abnormal `try`/`catch`, and unclear errors
- files, modules, classes, or functions with too many concerns
- mixed data models and implementation, inline domain constants, and platform-specific logic that should stay portable
- comments that explain code instead of a non-obvious reason, or defensive checks that add no safety
- files over 1,000 lines only when this branch crosses the limit or adds a new concern

Prefer fewer high-confidence findings and the smallest fix that preserves intended behavior. Keep trivial local types and constants local. Use the existing project structure.

## Loop

1. Freeze scope. Finish when the scope lock lists the goal, non-goals, base commit, target manifest, changed behavior, required checks, and allowed optional checks.
2. Start cycle `N`. Count every Sentinel request. Never ask Sentinel when `N` is greater than four.
3. Run `thermo-nuclear-code-quality-review` and `deslop` on the target. Add each finding to the decision ledger.
4. Read the complete current target and run every required check. Record each command and result. Run optional targeted checks when they help. Skip development servers and builds. Fix a branch-caused required-check failure only when the fix stays in scope. After every fix, reread the target and rerun every required check. Stop with `Verdict: Cannot approve` for an unavailable, unrelated, or unresolved required-check failure.
5. Snapshot the target. Ask a fresh, zero-shot Sentinel for a review. Give it the scope lock, target manifest, review bar, decision ledger, and snapshot. Do not give it a previous verdict.
6. Compare the target with the snapshot. If it is unchanged, continue to step 7. If it changed, invalidate the verdict. If `N` is four, stop with `Verdict: Cannot approve`. Otherwise, increment `N` and return to step 2.
7. Treat only `Verdict: Approved` as approval. Before accepting it, reread the complete target. If it changed, invalidate the verdict. If `N` is four, stop with `Verdict: Cannot approve`. Otherwise, increment `N` and return to step 2. If it is unchanged, confirm that all changed behavior has passing checks, all findings have a disposition, and no structural complexity or code slop remains in scope.
8. For every other verdict, classify each finding as `fixed`, `rejected-out-of-scope`, or `blocked-decision`. Fix only actionable findings within the scope lock. Record evidence and the reason for every rejection. Stop with `Verdict: Cannot approve` for a blocked decision or scope expansion.
9. After a non-approved review, stop with `Verdict: Cannot approve` when `N` is four, the same root cause returns, a rejected finding returns after the decision ledger was provided, or the transition since the previous review made no progress. Skip the no-progress test for cycle 1. A transition makes progress when it fixes a finding, changes a required check from failed to passed, or adds a disposition that the next Sentinel accepts. If no stop condition applies, increment `N` and start the next cycle.

Use the Sentinel output format for the final report:

1. `Verdict: Approved`, `Verdict: Approved with nits`, `Verdict: Changes requested`, or `Verdict: Cannot approve`
2. `Summary:` with at most three bullets
3. `Findings:` with each unresolved finding's priority, file, line, issue, impact, and fix, or `None`
4. `Caveats:` with skipped files, checks, or uncertainty

Include the cycle count, check results, fixed findings, rejected findings, and blockers in the summary or caveats.
