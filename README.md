# Botong — Two Hands, One Mind

Botong runs a two-role coding loop in one conversation: a work agent implements and verifies the change, while an independent review agent challenges it. The same two agents reconcile numbered findings until the review is clear or a real user decision is needed.

The workflow preserves existing worktree changes, keeps the reviewer read-only, accepts evidence-based pushback, and applies Ponytail's root-cause and minimum-complete-change principles.

## Requirements

Botong needs a host that can create and resume two addressable agents and return both agents' results to the coordinator. This repository packages adapters for Claude Code and Codex; it does not claim packaged support for other hosts.

## Install

These commands assume this repository is published at `github.com/h0ngcha0/botong`.

### Claude Code

Run these as two separate prompts:

```text
/plugin marketplace add h0ngcha0/botong
```

```text
/plugin install botong@botong
```

### Codex

```bash
codex plugin marketplace add h0ngcha0/botong
codex plugin add botong@botong
```

Start a new conversation after installation so the skill is discovered.

## Development

The Python validators require PyYAML. From the repository root, create a disposable environment and run all package checks:

```bash
(
  botong_validator_dir="$(mktemp -d)"
  trap 'rm -rf "$botong_validator_dir"' EXIT
  python3 -m venv "$botong_validator_dir"
  "$botong_validator_dir/bin/python" -m pip install PyYAML
  "$botong_validator_dir/bin/python" "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" skills/botong
  "$botong_validator_dir/bin/python" "${CODEX_HOME:-$HOME/.codex}/skills/.system/plugin-creator/scripts/validate_plugin.py" .
  claude plugin validate .
)
```

Load the checkout directly in Claude Code:

```bash
claude --plugin-dir .
```

For direct Codex discovery, create a local skill link and start a new conversation:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
ln -s "$PWD/skills/botong" "${CODEX_HOME:-$HOME/.codex}/skills/botong"
```

If that destination already exists, confirm it resolves to this checkout; do not overwrite it. In the new conversation, ask Codex to use Botong on a coding change.

## License

[MIT](LICENSE) © 2026 h0ngcha0
