# Host adapters

Select one adapter from tools exposed in the current session. The invoking Hubo skill defines the two role names, the standby role, report labels, and write boundaries. Preserve them and the numbered reconciliation protocol regardless of host.

## Codex

Select this adapter when `spawn_agent`, `followup_task`, and `wait_agent` are available.

| Hubo action | Codex mapping |
| --- | --- |
| Create roles | Call `spawn_agent` exactly twice from the coordinator, using the two names required by the invoking skill. |
| Standby | Give the role designated by the invoking skill an initial standby task that returns `READY` without inspecting files. |
| Resume a role | Call `followup_task` with the existing role ID; never replace the role for a new round. |
| Wait | Call `wait_agent` for a meaningful completion; do not poll status. |
| Relay | Mirror every complete top-level role report in the current conversation with its mode-defined role and round label; repeat it in the final conversation transcript, never a file. |
| Descendants | Let a role call `spawn_agent` only for bounded independent work; its descendants inherit that role's write boundary. |

Use `send_message` only for information that does not need to trigger a stopped role; use `followup_task` for the next work or review round.

## Claude Code

Select this adapter when `Agent` and `SendMessage` are available.

| Hubo action | Claude Code mapping |
| --- | --- |
| Create roles | Invoke `Agent` exactly twice from the coordinator, using the two names required by the invoking skill. Request background execution when the exposed schema supports it. |
| Standby | Prompt the mode-designated standby role to return `READY` without inspecting files. A stopped agent remains available for later rounds. |
| Resume a role | Call `SendMessage({to: agentId})` with the next round; do not use a resume argument on `Agent`. |
| Wait | Use completion notifications. For background output, use `Read` on the output path supplied by the notification; do not use deprecated `TaskOutput`. |
| Relay | Mirror every complete top-level role report in the current Claude conversation with its mode-defined role and round label; repeat it in the final conversation transcript, never a file. |
| Descendants | Permit them only when the child exposes `Agent`; otherwise keep bounded subwork in the owning top-level role. |

Treat a role as mechanically read-only only when a narrow allowlist or sandbox blocks every mutation path, including file editors, notebook editors, shell/process writes, and write-capable external tools. Otherwise enforce read-only as a protocol boundary, snapshot the worktree before and after each read-only turn, and treat mutations as open protocol violations. Allow read-only shell commands only for non-destructive inspection and verification.

## GitHub Copilot CLI

Select this adapter when `task`, `list_agents`, `read_agent`, and `write_agent` are available.

| Hubo action | GitHub Copilot CLI mapping |
| --- | --- |
| Create roles | Use `task` exactly twice in background mode, using the two names required by the invoking skill. |
| Standby | Prompt the mode-designated standby role to return `READY` without inspecting files while remaining available for follow-up turns. |
| Resume a role | Call `write_agent` with the existing agent ID; never replace the role for a new round. |
| Wait | Use background completion notifications. Use `read_agent` once to retrieve a completed turn when needed; do not poll it. |
| Relay | Mirror every complete top-level role report in the current Copilot conversation with its mode-defined role and round label; repeat it in the final conversation transcript, never a file. |
| Descendants | Permit them only when the child exposes `task`; otherwise keep bounded subwork in the owning top-level role. |

Keep both background agents alive for the multi-turn reconciliation loop. Resolve their IDs from the initial `task` results or `list_agents`; use `write_agent` for every later round.

## OpenClaw

Select this adapter when `sessions_spawn`, `sessions_send`, and `sessions_yield` are available.

| Hubo action | OpenClaw mapping |
| --- | --- |
| Create roles | Call `sessions_spawn` exactly twice with `mode: "run"`, `cleanup: "keep"`, and the two labels required by the invoking skill. Record each returned `childSessionKey`. |
| Standby | Give the mode-designated standby role an initial task that returns `READY` without inspecting files. |
| Resume a role | Call `sessions_send` with the recorded child session key and wait for its response; never create a replacement session for a new round. |
| Wait | After the initial spawns, call `sessions_yield` and consume completion events. For later rounds, use `sessions_send` with a non-zero timeout; do not poll session status. |
| Relay | Mirror every complete top-level role report in the current OpenClaw conversation with its mode-defined role and round label; repeat it in the final conversation transcript, never a file. |
| Descendants | Permit them only when that child exposes `sessions_spawn`; otherwise keep bounded subwork in the owning top-level role. |

Use `sessions_history` only when a completion payload is incomplete or must be recovered; it is not the waiting mechanism.

## Capability fallbacks

- If background agents are unavailable, create the standby role first for its immediate return, then run the active role in the foreground. Resume both by their existing IDs.
- If descendants are unavailable or capacity is exhausted, skip them; they are optional.
- If the host cannot create two addressable direct children, resume the same child, or return child results to the coordinator, do not claim Hubo convergence. Ask whether the user accepts a sequential single-agent fallback instead.
- If every mutation path cannot be blocked, keep read-only as a protocol boundary rather than claiming mechanical enforcement. Before and after each read-only turn, compare content-sensitive evidence for the user-specified mutable target, including relevant tracked, untracked, ignored, and nested-repository state. If no worktree represents the target, mark the worktree check not applicable and require mechanical read-only access or equivalent target-specific before/after evidence; otherwise report that the boundary is unverifiable.
- For another host, add an adapter only after verifying its real create, resume/message, wait/result, and permission capabilities. Map those semantics without changing the core workflow.
