# Host adapters

Select one adapter from tools exposed in the current session. Preserve Hubo's role boundaries and numbered reconciliation protocol regardless of host.

## Codex

Select this adapter when `spawn_agent`, `followup_task`, and `wait_agent` are available.

| Hubo action | Codex mapping |
| --- | --- |
| Create roles | Call `spawn_agent` exactly twice from the coordinator, naming the direct children `work_agent` and `review_agent`. |
| Reviewer standby | Give `review_agent` an initial standby task that returns without inspecting files. |
| Resume a role | Call `followup_task` with the existing role ID; never replace the role for a new round. |
| Wait | Call `wait_agent` for a meaningful completion; do not poll status. |
| Relay | Use the child result delivered to the parent, then write the concise role-labeled conclusion in the current conversation. |
| Descendants | Let a role call `spawn_agent` only for bounded independent work; its descendants inherit that role's write boundary. |

Use `send_message` only for information that does not need to trigger a stopped role; use `followup_task` for the next work or review round.

## Claude Code

Select this adapter when `Agent` and `SendMessage` are available.

| Hubo action | Claude Code mapping |
| --- | --- |
| Create roles | Invoke `Agent` exactly twice from the coordinator, naming the direct subagents `work_agent` and `review_agent`. Request background execution when the exposed schema supports it. |
| Reviewer standby | Prompt `review_agent` to return `READY` without inspecting files. A stopped agent remains the reviewer for later rounds. |
| Resume a role | Call `SendMessage({to: agentId})` with the next round; do not use a resume argument on `Agent`. |
| Wait | Use completion notifications. For background output, use `Read` on the output path supplied by the notification; do not use deprecated `TaskOutput`. |
| Relay | Summarize each returned agent conclusion with the required role label in the current Claude conversation. |
| Descendants | Permit them only when the child exposes `Agent`; otherwise keep bounded subwork in the owning top-level role. |

Treat reviewer isolation as mechanically read-only only when a narrow allowlist or sandbox blocks every mutation path, including file editors, notebook editors, shell/process writes, and write-capable external tools. In all cases, snapshot the worktree before and after every review and treat any reviewer mutation as an open protocol violation for the worker to reconcile. Allow reviewer shell commands only for non-destructive inspection and verification.

## Capability fallbacks

- If background agents are unavailable, create the reviewer first for its immediate standby return, then run the worker in the foreground. Resume both by their existing IDs.
- If descendants are unavailable or capacity is exhausted, skip them; they are optional.
- If the host cannot create two addressable direct children, resume the same child, or return child results to the coordinator, do not claim Hubo convergence. Ask whether the user accepts a sequential single-agent implementation and self-review instead.
- If every mutation path cannot be blocked, keep read-only as a protocol boundary rather than claiming mechanical enforcement; rely on the mandatory snapshot comparison.
- For another host, add an adapter only after verifying its real create, resume/message, wait/result, and permission capabilities. Map those semantics without changing the core workflow.
