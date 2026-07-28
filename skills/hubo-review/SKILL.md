---
name: hubo-review
description: Reviews without editing, then has a critic challenge every finding and missed risk until the review is reconciled. Use only when the user explicitly invokes or selects Hubo Review for code, diffs, pull requests, tests, designs, or technical claims.
disable-model-invocation: true
---

# Hubo Review

Review the user's specified programming target without changing it. Create exactly two persistent top-level roles:

- `review_agent`: owns the canonical review.
- `critic_agent`: tries to disprove the review, expose false positives, and find material omissions. Start this role in standby.

## Host mapping

Use the first available host:

- Codex: create with `spawn_agent`, resume with `followup_task`, and wait with `wait_agent`.
- Claude Code: create with `Agent`, resume with `SendMessage`, and consume completion notifications.
- GitHub Copilot CLI: create background roles with `task`, resume with `write_agent`, and consume completion notifications or `read_agent`.
- OpenClaw: create kept sessions with `sessions_spawn`, resume with `sessions_send`, and wait with `sessions_yield`.

Create each role once and reuse its ID for every round. The critic's initial task returns only `READY`. Optional descendants inherit their parent's read-only boundary. If the host cannot create and resume two addressable roles, ask whether the user accepts a sequential single-agent fallback.

Both lineages are read-only. Block mutation tools mechanically when possible. Otherwise compare content-sensitive before/after evidence for the user-specified mutable target around every role turn; use target-specific evidence when no worktree represents it and report when the boundary cannot be verified.

## Shared core principles

Apply this complete list to the coordinator, both roles, and every descendant. Include it in both initial role prompts; it remains binding in every later round.

1. **[Karpathy-inspired coding discipline](https://github.com/multica-ai/andrej-karpathy-skills).**
   Think before acting: surface material assumptions, ambiguity, tradeoffs, and warranted pushback. Prefer the simplest complete approach, make surgical changes, derive verifiable acceptance criteria, and run proportionate checks.
2. **[Ponytail](https://github.com/DietrichGebert/ponytail).**
   Understand the relevant flow first, then stop at the first rung that solves the task: do nothing when nothing is needed; reuse the codebase; use the standard library; use a native platform feature; use an already-installed dependency; use one line; only then require the minimum new code. Prefer root-cause fixes and never simplify away correctness, safety, or explicit requirements.
3. **Evidence over assertion.**
   Base findings and challenges on the request, repository instructions, actual target, diff, reproducible checks, or primary-source documentation. State uncertainty instead of inventing certainty.
4. **Independent challenge, shared objective.**
   The critic tries to falsify the review. The reviewer considers every critique but may defend a finding with evidence. Neither role raises taste-only preferences, speculative abstractions, or work outside the request.
5. **User attention is the last resort.**
   Resolve everything that inspection, tests, or primary evidence can decide. Escalate only a genuine requirement, authority, or technical-direction choice.

The links above provide attribution only. The inlined principles are the operative instructions; never rely on a role opening an external URL.

## Loop

1. Give both roles the request, target, repository instructions, review criteria, and the complete shared core principles above.
2. Require `REVIEW ROUND 0`: inspected scope, exact checks, limitations, and `CLEAR` or findings:
   `F<K> | severity | location | claim | evidence | required outcome`
3. Send the complete review to the critic. Require `CRITIQUE ROUND N`: `CLEAR` or:
   `C<N>.<K> | finding or omission | challenge | evidence | required reconciliation`
4. The critic verifies correctness, severity, scope, required outcomes, duplicates, false positives, and missed risks against the actual target.
5. Send every critique unchanged to the same reviewer. Require `REVIEW RESPONSE N` with `ACCEPTED`, `REVISED`, `WITHDRAWN`, `DEFENDED`, or `NEEDS_USER` for each ID and the complete updated canonical review.
6. Resume the same critic to mark prior critiques `CLOSED`, `WITHDRAWN`, or `OPEN`, inspect the whole review again, and continue without an arbitrary round limit.

Findings must be actionable and tied to correctness, security, regression risk, maintainability, unnecessary complexity, or the user's requirements. Exclude taste and speculative work.

Mirror every complete top-level report into the current conversation as `[Main reviewer · ...]` or `[Review critic · ...]`. Stop only when the critic returns `CLEAR` with every critique reconciled, or a genuine requirement or technical-direction choice needs the user. If the same evidence-based disagreement remains open for two consecutive rounds after exhausting in-scope checks, ask the user for the smallest decision needed. Return the final canonical review followed by the complete chronological `REVIEWER/CRITIC EXCHANGE TRANSCRIPT`. Never write it to a file.
