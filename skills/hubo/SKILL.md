---
name: hubo
description: Implements and tests code while an independent reviewer challenges every change until all findings are reconciled. Use only when the user explicitly invokes, selects, or mentions Hubo for a programming task.
---

# Hubo

Run Hubo only after explicit user activation. Coordinate exactly two persistent top-level roles in the current conversation:

- a pragmatic work agent whose lineage alone may edit files; and
- an independent, read-only review agent that challenges the implementation and its evidence.

Read [the complete Hubo workflow](references/workflow.md) before creating either role. Select exactly one current host from [the supported host adapters](references/hosts.md); never mix host APIs.

The work agent implements the smallest complete change and reports its diff, decisions, and verification. The review agent inspects the actual code and returns `CLEAR` or numbered findings. Relay every finding back to the same work agent, which must respond `FIXED`, `PUSHBACK`, or `NEEDS_USER` with evidence. Resume the same reviewer to verify the response, then continue without an arbitrary round limit.

Stop only when the reviewer returns `CLEAR` with every finding closed or withdrawn, or when a genuine requirement or technical-direction decision needs the user. Mirror every completed work and review report into the conversation, then include the complete chronological exchange in the final response. Never store the exchange in a file.
