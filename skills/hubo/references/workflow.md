# Hubo — Two Hands, One Mind

Coordinate one writing lineage and one independent, read-only reviewing lineage in the current conversation. Keep a complete, chronological trail of their top-level role reports while they iterate to convergence.
Like Zhou Botong's Technique of Ambidexterity (雙手互搏), the work and review lineages act as two independent hands: one builds while the other challenges.
Treat text accompanying the explicit invocation as the task. If none is provided, ask for the task before creating either role.

## Apply the shared core principles

Apply this ordered list to the coordinator, both top-level roles, and every descendant. Include the complete list in both initial role prompts; it remains binding in every later round.

1. **[Karpathy-inspired coding discipline](https://github.com/multica-ai/andrej-karpathy-skills).**
   - Think before coding: surface material assumptions, ambiguity, tradeoffs, and warranted pushback.
   - Prefer the simplest complete solution: add no speculative features, abstractions, or configurability.
   - Make surgical changes: touch only task-relevant code, match existing style, and clean up only what the change makes obsolete.
   - Work toward verifiable goals: derive acceptance criteria, use a brief plan for multi-step work, and loop until proportionate checks pass.
2. **[Ponytail](https://github.com/DietrichGebert/ponytail).**
   Understand the relevant flow first, then stop at the first rung that solves the task: do nothing when nothing is needed; reuse the codebase; use the standard library; use a native platform feature; use an already-installed dependency; use one line; only then write the minimum new code. Fix root causes in shared paths rather than symptoms. Never simplify away trust-boundary validation, data-loss prevention, security, accessibility, hardware calibration, or explicit requirements. Leave one small runnable check for new non-trivial logic.
3. **Evidence over assertion.**
   Base implementation and review claims on the actual request, repository instructions, code, diff, reproducible checks, or primary-source documentation. State uncertainty instead of inventing certainty.
4. **Independent challenge, shared objective.**
   The reviewer tries to falsify the implementation without editing it. The worker considers every finding but may push back with evidence. Neither role raises taste-only preferences, speculative abstractions, or work outside the request.
5. **User attention is the last resort.**
   Resolve everything that code, inspection, tests, or primary evidence can decide. Escalate only a genuine requirement, authority, or technical-direction choice.

When instructions conflict, follow system and host constraints first, then the user's request and repository instructions. Never use simplicity to override correctness or safety.

## Establish the contract

1. Read the request and all applicable repository instructions, including `AGENTS.md`.
2. Preserve user-owned worktree changes. Never reset, checkout, clean, stash, delete, or rewrite unrelated changes.
3. State only assumptions that affect the result. Derive concrete acceptance criteria and proportionate verification from the request.
4. Honor all active skills and instructions together with the shared core principles above.

## Select the host adapter

Before creating either role, read [hosts.md](hosts.md) completely. Detect the current host from the agent tools actually exposed, select exactly one mapped adapter, and follow its lifecycle and fallback rules. Never mix tool names from different hosts or guess an unavailable capability.

## Keep the worker/reviewer exchange reviewable

Mirror every completed top-level role report into the current user conversation without summarizing it away:

- `[Work agent · round N]` for `WORK ROUND N`;
- `[Review agent · round N]` for `REVIEW ROUND N`; and
- `[Work agent · response N]` for `WORK RESPONSE N`.

Include the full structured report, findings, dispositions, commands and results, evidence, and decisions. Preserve finding IDs and wording across relays. Cross-role communication must pass through the coordinator.

At final handoff, render a chronological `WORKER/REVIEWER EXCHANGE TRANSCRIPT` directly in the final conversation response, containing every mirrored role report from every round. Never write this transcript to a file. Do not include coordinator prompts, wait notifications, tool chatter, or descendant chatter; each top-level role is responsible for incorporating material delegated evidence into its own report. Redact only credentials, secrets, or system-protected data, and leave an explicit `[REDACTED: <reason>]` marker.

Do not request or expose hidden chain-of-thought, private scratchpads, or model-internal reasoning. The reviewable trace consists of observable messages, actions, evidence, and outcomes.

## Create exactly two top-level roles

Spawn exactly two direct children in the same collaboration tree, once:

- `work_agent`: pragmatic senior implementer. Its lineage is the only lineage allowed to edit files. It owns investigation, implementation, cleanup caused by its changes, and verification.
- `review_agent`: skeptical senior reviewer. Its entire lineage is read-only. It inspects the implementation and evidence, runs only non-destructive checks, and reports correctness, security, regression, maintainability, and requirement gaps.

Do not create a third top-level child or replace either role during the loop. Either role may spawn descendants for bounded independent subtasks when useful and capacity permits; descendants stay categorized by their parent lineage and inherit its write boundary. Do not hardcode a model or provider.

Give both roles the original request, acceptance criteria, applicable repository instructions, and the complete shared core principles. In the review agent's initial task, tell it to stand by without inspecting the worktree until it receives a completed work round. Start the work agent immediately. With limited concurrency, prefer the two persistent roles over descendants.

Use the selected adapter's mapped wait operation for meaningful completions and its mapped resume/message operation to continue the same role each round. Do not poll with repeated status calls. Never create files merely to exchange review notes; agent messages are the ledger.

## Run the initial work round

Tell `work_agent` to:

1. Capture the initial worktree state and distinguish pre-existing changes from task changes.
2. Apply the shared core principles: trace the relevant flow and callers before editing, climb the Ponytail ladder, then implement the minimum complete change.
3. Run verification proportional to risk. Use existing focused tests first; add one small runnable check for new non-trivial logic when the repository lacks coverage.
4. Return `WORK ROUND 0` with:
   - outcome and changed files;
   - key design decisions or assumptions;
   - exact verification commands and results;
   - remaining uncertainty or `NEEDS_USER`, with the decision required.

Mirror the complete returned `WORK ROUND 0` report into the current user conversation.

Do not wait for user input unless the worker identifies a genuine escalation under the rules below.

## Review and reconcile

For review round `N`, send the reviewer the original request, acceptance criteria, current worktree scope, and the latest worker report. Require inspection of the actual files and diff rather than trusting the report.

The reviewer returns `REVIEW ROUND N` with either `CLEAR` or numbered findings:

`R<N>.<K> | severity | file:line | claim | evidence | required outcome`

Findings must apply the shared core principles and be actionable and tied to a requirement, defect, regression risk, security issue, or unnecessary complexity. The reviewer must challenge over-engineering as well as under-engineering, and must not demand code below the first Ponytail rung that safely satisfies the task. Exclude taste-only preferences and speculative abstractions. Preserve an existing finding ID until it is closed or withdrawn.

Mirror the complete returned `REVIEW ROUND N` report into the current user conversation.

If findings remain, send every open finding unchanged to `work_agent`. Require `WORK RESPONSE N` with one disposition per ID:

- `FIXED`: make the edit and cite the diff plus verification.
- `PUSHBACK`: leave the code unchanged and provide concrete evidence that the finding is invalid, outside scope, or worse than the current design.
- `NEEDS_USER`: identify the smallest product, requirement, or technical-direction choice that only the user can make.

Require the worker to rerun affected checks after edits. Mirror the complete returned `WORK RESPONSE N` into the current user conversation.

Send the response and current worktree back to the same reviewer with the selected adapter's mapped resume/message operation. The reviewer must:

1. Verify fixes and pushback against the actual code and evidence.
2. Mark each prior ID `CLOSED`, `WITHDRAWN`, or `OPEN`.
3. Review the whole task state for regressions and new issues, not only the response.
4. Assign new IDs to new findings and return the next `REVIEW ROUND`.

Continue the worker-response/re-review cycle without an arbitrary round limit.

## Escalate only real decisions

Ask the user only when:

- a requirement or technical-direction choice materially changes the correct implementation;
- required authority or unavailable external state cannot be worked around safely; or
- the same material finding remains open for two consecutive rounds because both roles repeat assertions without new code, a reproducible check, primary-source evidence, or other concrete evidence.

Before escalating, exhaust safe in-scope inspection and verification. Present the exact unresolved finding, both positions, evidence already gathered, and the smallest decision with its tradeoff. Do not escalate an evidence-based disagreement merely because it is difficult.

## Converge and hand off

Convergence requires all of the following:

- every finding is `CLOSED` or `WITHDRAWN`;
- the reviewer returns `CLEAR` after inspecting the final code;
- task-relevant verification passes at a level proportionate to risk; and
- limitations or checks that could not be run are explicitly recorded.

Then mirror both final role conclusions and return one compact user-facing handoff followed in the same conversation response by the complete `WORKER/REVIEWER EXCHANGE TRANSCRIPT`: outcome, files changed, verification, reconciled review result, round span, and any disclosed limitation. Do not write the transcript to disk, omit any completed role report, or make additional edits after the final clear review.
