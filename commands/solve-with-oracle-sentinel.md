Solve the user task with one Oracle plan, at most one Oracle finding review, and a strict Sentinel approval gate. The main agent owns all edits and checks. Oracle and Sentinel do not edit files.

## Explore

1. Read the code that controls the reported behavior.
2. Trace direct callers, tests, contracts, and canonical helpers.
3. Record the original problem, constraints, non-goals, baseline `HEAD`, pre-existing worktree changes, relevant files, required checks, and one small hypothesis that cites code evidence.
4. Mark each unverified claim as an assumption.
5. Do not edit files before Oracle returns its plan.

## Ask Oracle

1. Ask one new Oracle instance that has no prior context.
2. Give Oracle a complete brief with the task, constraints, non-goals, relevant files, key code, evidence, assumptions, and hypothesis.
3. Ask Oracle to test the hypothesis and return one specific in-scope solution.
4. Require an ordered implementation plan with affected files and functions, behavior to preserve, checks, risks, and stop conditions.
5. If Oracle fails or does not return the complete plan, stop with `Verdict: Cannot approve`. Use the failure report with zero Sentinel cycles and state that the post-Sentinel Oracle request was not used. Do not implement without the plan.
6. Use the original task as the authority when it conflicts with the Oracle plan.

## Implement

1. Implement Oracle's in-scope solution.
2. Record each necessary deviation from the plan and its reason.
3. Read the complete changed target.
4. Run the required targeted checks.
5. Do not run a build or start a development server unless the user asked for it.

## Sentinel loop

1. Set the cycle count to zero.
2. Set the post-Sentinel Oracle budget to one request.
3. Before each Sentinel request, add one to the cycle count.
4. Never make more than four Sentinel requests.
5. Snapshot `HEAD`, the current diff, untracked task files, and check results.
6. Ask a new Sentinel instance that has no prior context to review the snapshot.
7. Give Sentinel a complete brief with the original problem, constraints, non-goals, Oracle plan, current target files, current diff, related callers and tests, check results, and finding dispositions.
8. Do not give Sentinel a previous verdict.
9. Require Sentinel to review the complete current target, not only prior findings.
10. Treat a Sentinel response as complete only when it has exactly one standalone `Verdict:` line, one `Summary:` section with at most three bullets, one `Findings:` section with `None` or valid finding entries, and one `Caveats:` section. Treat a response with a missing or duplicate required part as malformed.
11. For each non-approval finding, require Sentinel to return:
    - `Finding ID: S<cycle>-F<sequence>`
    - `Root cause: <file:line and symbol> | unknown`
    - `Behavior authority: <task clause, test, contract, or file:line> | missing | conflict:<citations>`
    - `Minimal fix candidates:` followed by one or more `<files and symbols> | <observable behavior effect> | <owning module>` entries, or `unknown`
    - `Preservation conflict: none | <Oracle plan step, test, caller, or contract citation>`
12. Require Sentinel to combine syntax-only alternatives into one fix candidate. Do not ask Sentinel for a complexity label, an Oracle recommendation, or a confidence score.
13. Count a failed, incomplete, or malformed Sentinel request as one cycle. For cycles one through three, record it as `blocked: sentinel-request-failed` or `blocked: sentinel-response-malformed`, then stop without Oracle. Do not infer missing routing fields.
14. Accept approval only when a complete Sentinel response has the exact standalone line `Verdict: Approved`.
15. Treat every other result as non-approval.
16. Accept approval only when the target matches the snapshot and all required checks pass.

### Finding routing

For cycles one through three, assign each valid non-approval finding one state:

- `pending-direct`: The finding has one known root cause, one explicit behavior authority, one concrete minimal fix candidate, and no preservation conflict.
- `pending-oracle`: The finding cites conflicting behavior authorities, more than one behaviorally different valid fix candidate, or a preservation conflict.
- `fixed`: The main agent changed the target, read it, and reran all required checks.
- `rejected-invalid`: Code or check evidence disproves the finding or its stated impact.
- `rejected-out-of-scope`: Evidence shows that the finding is pre-existing, not task-caused, or requires a change outside the frozen goal.
- `blocked`: The root cause, behavior authority, or concrete fix is missing, or the finding cannot progress because input is missing, an Oracle request failed, the Oracle budget is exhausted, or a required fix conflicts with the frozen goal.

Do not route by priority, file count, line count, estimated effort, or terms such as complex, subtle, risky, or architectural.

### Oracle finding review

1. After classification, stop without Oracle if any finding is `blocked`.
2. If no finding is `blocked` and one or more findings are `pending-oracle`, use one new Oracle instance before changing the reviewed snapshot.
3. Batch all `pending-oracle` findings from that Sentinel cycle into the same request.
4. Give Oracle the original task, frozen goal, constraints, non-goals, initial Oracle plan, plan deviations, current snapshot, complete target, diff, related callers and tests, check results, complete Sentinel response, finding dispositions, and each escalated finding with all routing fields.
5. Ask Oracle to test each finding. Do not ask it to approve the implementation or re-plan the complete task.
6. Require one result for each finding with `Finding ID`, `Assessment: supported | unsupported | out-of-scope | needs-input`, `Evidence`, `Recommended candidate`, `Smallest action`, `Behavior to preserve`, and `Required checks`.
7. Do not count the Oracle request as a Sentinel cycle and do not reset the Sentinel cycle count.
8. Never retry the Oracle request. Never make more than one post-Sentinel Oracle request.
9. If the Oracle request fails, is incomplete, or is malformed, move every batched finding to `blocked: oracle-request-failed` and stop.
10. After a complete Oracle response, verify its evidence and map each assessment:
    - Map verified `supported` with one concrete candidate and no remaining conflict to `pending-direct`.
    - Map verified `unsupported` to `rejected-invalid`.
    - Map verified `out-of-scope` to `rejected-out-of-scope`.
    - Map `needs-input`, invalid evidence, no concrete candidate, or remaining ambiguity to `blocked`.
11. Oracle advice does not replace the next Sentinel review. The original task remains the authority.

### Resolution and stop rules

1. Resolve each `pending-direct` finding as `fixed`, `rejected-invalid`, `rejected-out-of-scope`, or `blocked`.
2. Fix each actionable finding that the task caused and that stays within the frozen goal.
3. Do not change unrelated code to get approval.
4. After each fix, read the changed target and rerun all required checks.
5. Give each new Sentinel instance the finding disposition ledger without a previous verdict.
6. Start the next cycle only when no finding is `pending-direct` or `pending-oracle`.
7. Stop before cycle four only when Sentinel returns `Verdict: Approved` or a finding becomes `blocked`.
8. If a new finding needs Oracle after the post-Sentinel Oracle budget is used, mark it `blocked: oracle-budget-exhausted` and stop.
9. Do not ask Oracle after cycle four because no Sentinel cycle remains to verify a resulting fix.
10. If cycle four fails, is malformed, or does not return `Verdict: Approved`, record the raw result and failure as a finding, then use `Verdict: Cannot approve` as the terminal verdict.
11. If the loop stops without approval, do not start another review. Return the failure report.

## Final response

For an approved implementation, report the implementation, check results, cycle count, and Sentinel verdict.

If the initial Oracle fails or the loop stops without approval, output only these sections:

## Original problem
State the problem in one sentence.

## Implementation
State the solution and check results in one short paragraph. If implementation did not start, state that.

## Sentinel findings
List the latest findings, their states, and the stop reason in one-line bullets. For an initial Oracle failure, list the Oracle blocker. Write `None` when there are no findings.

## Sentinel verdict
Use `Verdict: Cannot approve`. State the latest raw Sentinel verdict or `not requested`, the Sentinel cycle count, and whether the post-Sentinel Oracle request was used.
