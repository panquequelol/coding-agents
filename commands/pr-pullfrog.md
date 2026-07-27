Review the current PR with Pullfrog until no actionable, in-scope findings remain.

## Authority

Pullfrog sees the code, diff, PR description, and your latest review comment. You have the full task and implementation context. Your scope, intended behavior, and trade-offs override Pullfrog.

Do not expand the PR to satisfy Pullfrog.

## Scope lock

Before the first review, extract the PR goal and non-goals from the task and PR description. Freeze them for every cycle.

Treat a Pullfrog request as actionable only if all are true:

1. It is required by the original PR goal or fixes a regression introduced by this PR.
2. It affects behavior changed by this PR.
3. It does not add a feature, refactor, cleanup, hardening, or unrelated issue.

If any condition fails, reject the request as out of scope. Do not change code, even if the request is technically correct.

## Cycle

1. Find the open PR for the current branch.
2. Read one snapshot of unresolved conversations started by Pullfrog.
3. Check Pullfrog's review body, top-level PR comments, inline threads, and any commits Pullfrog added.
4. Only act on findings that apply to the current `HEAD`. Recheck stale findings against the current diff.
5. If Pullfrog added a commit, inspect it for correctness and scope before keeping it.
6. For each conversation:
   - Fix valid issues within this PR's scope.
   - For intentional behavior, known trade-offs, future work, stale issues, invalid issues, or out-of-scope requests, do not change code. Reply with a short reason and include the decision in the next Pullfrog context comment.
   - Leave unclear issues open and report them.
7. If a requested fix adds capability or changes user-visible behavior, stop and ask. Do not decide that inside the review loop.
8. Run relevant checks. If code changed, create one Conventional Commit commit and push the current branch. Do not create an empty commit.
9. If you addressed or rejected a conversation, post exactly one new PR comment:
   - Start the comment with `@pullfrog review this.`
   - Use one line and one paragraph only.
   - Keep it to 30 words or fewer. Mention addressed notes and why rejected requests remain out of scope.
   - Example: `@pullfrog review this. Addressed the timezone picker notes. The map order change is intentional and out of scope for this PR.`
10. If you posted a comment, wait at least six minutes.
11. If any unclear issue remains, stop after this cycle and report it. If you posted no comment, stop immediately.
12. Read Pullfrog's new review. Ignore old resolved conversations.
13. Repeat only when no unclear issues remain and new actionable, in-scope findings exist. For rejected findings, invoke Pullfrog once with the scope context. Stop if it asks for the same work again.

Run no more than four review cycles. Stop earlier when Pullfrog reports no issues or no actionable, in-scope findings remain. If any unclear or only out-of-scope requests remain, stop and report them. Before stopping, read the full diff from `main` to `HEAD`. Do not start a new cycle for a review with no new actionable issue.
