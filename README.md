<h1 align="center">Hubo</h1>

<p align="center">
  <strong>雙手互搏</strong><br>
  <em>Two agents. One codebase. No finding left unresolved.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/github/stars/h0ngcha0/hubo?style=flat-square&color=111111&label=stars" alt="GitHub stars">
  <img src="https://img.shields.io/badge/version-0.2.0-111111?style=flat-square" alt="Version 0.2.0">
  <img src="https://img.shields.io/badge/Claude_Code-supported-111111?style=flat-square" alt="Claude Code supported">
  <img src="https://img.shields.io/badge/Codex-supported-111111?style=flat-square" alt="Codex supported">
  <img src="https://img.shields.io/badge/license-MIT-111111?style=flat-square" alt="MIT license">
</p>

---

[Zhou Botong](https://en.wikipedia.org/wiki/Zhou_Botong) is one of wuxia's great eccentrics: a legendary martial artist with the temperament of a mischievous child. When ordinary practice becomes too ordinary, he invents something delightfully absurd — the **Technique of Ambidexterity** (雙手互搏).

He teaches each hand to act independently. One hand attacks; the other answers. Each uses a different martial art. The same person becomes both opponents, and the sparring continues until neither hand can surprise the other.

It sounds like a party trick. It is actually a discipline: split creation from criticism, give both sides room to think, and let disagreement expose what either side would miss alone.

Hubo puts that little duel inside your coding agent.

The name comes from **互搏** (*hùbó*): to spar with each other. Not two agents politely taking turns, and not a reviewer saying “looks good” from the doorway. One builds. One challenges. They exchange evidence until the code — not either agent's ego — wins.

## Before / after

Without Hubo, an agent implements the feature, runs a test, reviews its own reasoning, and hands the result to you. The author, tester, and reviewer all share the same blind spots.

With Hubo:

```text
your task
   ↓
work agent ── implementation + evidence ──▶ review agent
     ▲                                      │
     └──── fix or reasoned pushback ◀───────┘
                      repeat
                         ↓
              reconciled result — or you
```

You see the work only after every concrete finding is resolved, unless the remaining question genuinely requires your judgment.

## How it works

Hubo keeps two persistent roles in the same conversation:

| Hand | Role |
|---|---|
| **Work agent** | Understands the task, changes the code, tests it, and answers every review finding with a fix or evidence-based pushback. |
| **Review agent** | Stays independent and read-only, inspects the actual diff and verification evidence, and returns numbered, actionable findings. |
| **Coordinator** | Carries messages between them, preserves unresolved findings across rounds, and stops only at a real exit condition. |

The loop ends when:

- the reviewer clears the work and every finding is reconciled; or
- the agents identify a product, requirement, or technical-direction decision that only you can make.

The reviewer may spawn specialists. The worker may delegate implementation. But at the top level the shape remains the same: one hand makes, one hand tests the making.

Hubo is explicit-only. It does nothing until you invoke it; ordinary coding prompts remain ordinary.

## Install

### Claude Code

Run these as two separate prompts:

```text
/plugin marketplace add h0ngcha0/hubo
```

```text
/plugin install hubo@hubo
```

Then run `/reload-plugins` or start a new conversation.

### Codex

```bash
codex plugin marketplace add h0ngcha0/hubo
codex plugin add hubo@hubo
```

Restart Codex and start a new conversation so the skill is discovered.

## Usage

- **Claude Code:** `/hubo implement the change...` when the short alias is unambiguous, or `/hubo:hubo implement the change...`.
- **Codex CLI:** invoke `$hubo:hubo`, then provide the task.
- **ChatGPT or Codex desktop:** type `@`, select **Hubo**, then provide the task.

For example:

```text
/hubo implement optimistic updates for the cart
```

## Requirements

Hubo needs a host that can create and resume two addressable agents and return both agents' results to the coordinator. This repository currently packages adapters for Claude Code and Codex.

The workflow preserves pre-existing worktree changes, keeps the reviewer read-only, accepts justified pushback, and favors root-cause fixes over patches that merely silence a symptom.

## Repository layout

The host-specific wrappers are intentionally small:

- `skills/hubo/` and `.codex-plugin/` package Hubo for Codex.
- `claude/skills/hubo/` and `claude/.claude-plugin/` package it for Claude Code.
- `claude/shared/hubo.md` contains the shared workflow.
- `claude/shared/references/hosts.md` maps the workflow onto each host's agent primitives.

## Development

The Python validators require PyYAML. From the repository root, create a disposable environment and run all package checks:

```bash
(
  hubo_validator_dir="$(mktemp -d)"
  trap 'rm -rf "$hubo_validator_dir"' EXIT
  python3 -m venv "$hubo_validator_dir"
  "$hubo_validator_dir/bin/python" -m pip install PyYAML
  "$hubo_validator_dir/bin/python" "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" skills/hubo
  "$hubo_validator_dir/bin/python" "${CODEX_HOME:-$HOME/.codex}/skills/.system/plugin-creator/scripts/validate_plugin.py" .
  claude plugin validate .
  claude plugin validate ./claude
)
```

Load the checkout directly in Claude Code:

```bash
claude --plugin-dir ./claude
```

For direct Codex discovery, create a local skill link and start a new conversation:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
ln -s "$PWD/skills/hubo" "${CODEX_HOME:-$HOME/.codex}/skills/hubo"
```

If that destination already exists, confirm it resolves to this checkout; do not overwrite it. In the new conversation, ask Codex to use Hubo on a coding change.

## License

[MIT](LICENSE) © 2026 h0ngcha0
