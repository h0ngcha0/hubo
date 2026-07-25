# Hubo — Two Hands, One Mind

Hubo runs a two-role coding loop in one conversation: a work agent implements and verifies the change, while an independent review agent challenges it. The same two agents reconcile numbered findings until the review is clear or a real user decision is needed.

The workflow preserves existing worktree changes, keeps the reviewer read-only, accepts evidence-based pushback, and applies Ponytail's root-cause and minimum-complete-change principles.

## Usage

Hubo activates only when you explicitly select or invoke it. Ordinary programming prompts do not activate it.

- **Claude Code:** use `/hubo implement the change...` when the bare alias is unambiguous, or the canonical `/hubo:hubo implement the change...`.
- **Codex CLI or IDE:** select `$hubo:hubo` for the installed plugin. Reserve `$hubo` for a directly linked local skill, then provide the task.
- **ChatGPT or Codex desktop:** type `@`, select the Hubo skill, then provide the task.

## Requirements

Hubo needs a host that can create and resume two addressable agents and return both agents' results to the coordinator. This repository packages adapters for Claude Code and Codex; it does not claim packaged support for other hosts.

## Install

These commands assume this repository is published at `github.com/h0ngcha0/hubo`.

### Claude Code

Run these as two separate prompts:

```text
/plugin marketplace add h0ngcha0/hubo
```

```text
/plugin install hubo@hubo
```

### Codex

```bash
codex plugin marketplace add h0ngcha0/hubo
codex plugin add hubo@hubo
```

Start a new conversation after installation so the skill is discovered.

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
  claude plugin validate ./claude/.claude-plugin/plugin.json
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
