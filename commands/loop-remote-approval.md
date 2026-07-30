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
6. For each finding:
   - Fix valid issues within this PR's scope.
   - For deliberate behavior, a known trade-off, future work outside the frozen scope, a stale finding, an invalid finding, or an out-of-scope request, do not change code. If the finding has an inline conversation, reply in that thread with one direct, self-contained line. State the PR goal and why the request is out of scope or the behavior is intentional. Do not include URLs, issue references, or links.
   - For an inline rejection, after the reply succeeds, resolve that inline conversation with its review thread node ID. If the reply or resolution fails, leave the conversation open, do not request another review, and report the failure.
   - For a non-inline rejection, include its concrete rejection reason and the PR goal in the top-level PR comment. Do not include URLs, issue references, or links. Do not claim to resolve a thread that does not exist.
   - Leave unclear issues open and report them.
7. If the frozen scope does not establish whether a requested capability or user-visible behavior is intended, leave the conversation open, stop, and ask. Do not decide that inside the review loop.
8. Run relevant checks. If any relevant check fails, stop and report the failure. If code changed for an actionable finding, create one Conventional Commit commit and push the current branch only after all relevant checks pass. Do not create a commit for a rejected finding or an empty commit.
   - After pushing a valid fix, reply to each addressed inline conversation with one direct, self-contained line. State the PR goal and the fix. Say that the finding is resolved in the current PR. Do not include URLs, issue references, or links. After the reply succeeds, resolve the conversation with its review thread node ID. If the reply or resolution fails, leave it open, do not request another review, and report the failure.
9. If you addressed or rejected a finding, no unclear finding remains, and each addressed or rejected inline conversation is resolved, prepare exactly one aggregate review request for the cycle. Never post one request per finding, commit, thread, or fix.
10. Immediately before you post the request, refresh the PR comments, Pullfrog reviews, checks, and recent Actions runs. Treat Pullfrog as active if either condition is true:
   - A Pullfrog review or fix run is queued, pending, waiting, requested, or in progress.
   - The latest top-level `@pullfrog` request has no Pullfrog review submitted after it for that request's `HEAD`. This is an unanswered request, even if no run appears in the checks or Actions list.
   If Pullfrog is active, do not post or queue another request. Wait six minutes, refresh all four sources, and check again. Continue to wait while its run is active. If the run fails, stop and report it. If the run completes but no matching review appears, wait one final six-minute interval, refresh, then stop and report the unanswered request. Do not replace it with another request.
11. If Pullfrog is not active, refresh the comments, reviews, checks, and recent Actions runs one final time. If no active or unanswered request exists, post exactly one new top-level PR comment:
   - Start the comment with `@pullfrog review this.`
   - Use one line and one paragraph only.
   - Keep it to 30 words or fewer. If multiple non-inline rejections need separate reasons, include each reason in the same paragraph and prioritize complete reasons over the word limit. For addressed notes, state the useful change. For rejected requests, state the PR goal and give the concrete behavior reason. Do not include URLs, issue references, or links. Do not add process language, scope labels, or claims about what was not requested.
   - Before posting, remove any clause that only says what you reviewed, did not change, did not add, or kept in scope. Keep concrete fixes and necessary behavior reasons only.
   - Example: `@pullfrog review this. This PR keeps the selected timezone after refresh. The finding is resolved in the current PR.`
   - For a necessary rejection, use a concrete reason: `@pullfrog review this. This PR preserves map order for downstream rendering. Changing map order is outside the PR goal.`
12. Record the posted comment ID, timestamp, and `HEAD` as the cycle's only request. Do not post another request until Pullfrog submits a completed review after that timestamp for that `HEAD`. Wait at least six minutes before you check for that answer.
13. If any unclear issue remains, stop after this cycle and report it. If you prepared no request, stop immediately.
14. Read Pullfrog's new review. Ignore old resolved conversations.
15. Repeat only when no unclear issues remain and the new review contains a new actionable, in-scope finding. For rejected findings, invoke Pullfrog once after all rejected inline conversations are resolved and include the scope context. Treat the same request in a new thread as repeated work and stop.

Run no more than four review cycles. Stop earlier when Pullfrog reports no issues or no actionable, in-scope findings remain. If any unclear or only out-of-scope requests remain, stop and report them. Before stopping, read the full diff from `main` to `HEAD`. Do not start a new cycle for a review with no new actionable issue.
