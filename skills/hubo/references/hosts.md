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
| Relay | Mirror every complete top-level work/review report in the current conversation with its role and round label; repeat it in the final conversation transcript, never a file. |
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
| Relay | Mirror every complete top-level work/review report in the current Claude conversation with its role and round label; repeat it in the final conversation transcript, never a file. |
| Descendants | Permit them only when the child exposes `Agent`; otherwise keep bounded subwork in the owning top-level role. |

Treat reviewer isolation as mechanically read-only only when a narrow allowlist or sandbox blocks every mutation path, including file editors, notebook editors, shell/process writes, and write-capable external tools. In all cases, snapshot the worktree before and after every review and treat any reviewer mutation as an open protocol violation for the worker to reconcile. Allow reviewer shell commands only for non-destructive inspection and verification.

## GitHub Copilot CLI

Select this adapter when `task`, `list_agents`, `read_agent`, and `write_agent` are available.

| Hubo action | GitHub Copilot CLI mapping |
| --- | --- |
| Create roles | Use `task` exactly twice in background mode, naming the direct children `work_agent` and `review_agent`. |
| Reviewer standby | Prompt `review_agent` to return `READY` without inspecting files while remaining available for follow-up turns. |
| Resume a role | Call `write_agent` with the existing agent ID; never replace the role for a new round. |
| Wait | Use background completion notifications. Use `read_agent` once to retrieve a completed turn when needed; do not poll it. |
| Relay | Mirror every complete top-level work/review report in the current Copilot conversation with its role and round label; repeat it in the final conversation transcript, never a file. |
| Descendants | Permit them only when the child exposes `task`; otherwise keep bounded subwork in the owning top-level role. |

Keep both background agents alive for the multi-turn reconciliation loop. Resolve their IDs from the initial `task` results or `list_agents`; use `write_agent` for every later round.

## OpenClaw

Select this adapter when `sessions_spawn`, `sessions_send`, and `sessions_yield` are available.

| Hubo action | OpenClaw mapping |
| --- | --- |
| Create roles | Call `sessions_spawn` exactly twice with `mode: "run"`, `cleanup: "keep"`, and labels `work_agent` and `review_agent`. Record each returned `childSessionKey`. |
| Reviewer standby | Give `review_agent` an initial task that returns `READY` without inspecting files. |
| Resume a role | Call `sessions_send` with the recorded child session key and wait for its response; never create a replacement session for a new round. |
| Wait | After the initial spawns, call `sessions_yield` and consume completion events. For later rounds, use `sessions_send` with a non-zero timeout; do not poll session status. |
| Relay | Mirror every complete top-level work/review report in the current OpenClaw conversation with its role and round label; repeat it in the final conversation transcript, never a file. |
| Descendants | Permit them only when that child exposes `sessions_spawn`; otherwise keep bounded subwork in the owning top-level role. |

Use `sessions_history` only when a completion payload is incomplete or must be recovered; it is not the waiting mechanism.

## Capability fallbacks

- If background agents are unavailable, create the reviewer first for its immediate standby return, then run the worker in the foreground. Resume both by their existing IDs.
- If descendants are unavailable or capacity is exhausted, skip them; they are optional.
- If the host cannot create two addressable direct children, resume the same child, or return child results to the coordinator, do not claim Hubo convergence. Ask whether the user accepts a sequential single-agent implementation and self-review instead.
- If every mutation path cannot be blocked, keep read-only as a protocol boundary rather than claiming mechanical enforcement; rely on the mandatory snapshot comparison.
- For another host, add an adapter only after verifying its real create, resume/message, wait/result, and permission capabilities. Map those semantics without changing the core workflow.
