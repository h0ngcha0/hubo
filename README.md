<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.png">
    <img src="assets/logo.png" width="220" alt="Hubo, the two-handed old trickster">
  </picture>
</p>

<h1 align="center">Hubo</h1>

<p align="center">
  <strong>双手互搏</strong><br>
  <em>Two hands spar over every change. Only real decisions reach you.</em>
</p>

<p align="center">
  <sub><a href="README.zh-CN.md">简体中文</a></sub>
</p>

---

[Zhou Botong](https://en.wikipedia.org/wiki/Zhou_Botong) is one of [wuxia](https://en.wikipedia.org/wiki/Wuxia)'s great eccentrics: a legendary martial artist with the temperament of a mischievous child. When ordinary practice becomes too ordinary, he invents something delightfully absurd — the **Technique of Ambidexterity** (双手互搏).

He teaches each hand to act independently. One hand attacks; the other answers. Each uses a different martial art. The same person becomes both opponents, and the sparring continues until neither hand can surprise the other.

It sounds like a party trick. It is actually a discipline: split creation from criticism, give both sides room to think, and let disagreement expose what either side would miss alone.

Hubo puts that little duel inside your coding agent.

The name comes from **互搏** (*hùbó*): to spar with each other. Not two agents politely taking turns, and not a reviewer saying “looks good” from the doorway. Two hands spar over every change. Only real decisions reach you. They exchange evidence until the code — not either agent's ego — wins.

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

<div align="center">
<table>
  <tr>
    <td align="center"><strong>Works<br>with</strong></td>
    <td align="center"><img src="https://raw.githubusercontent.com/paperclipai/paperclip/d1b9448b57a8cfb0e8dbede9bbbc8874a9f66ad7/doc/assets/logos/openclaw.svg" width="32" alt="OpenClaw"><br><sub>OpenClaw</sub></td>
    <td align="center"><img src="https://raw.githubusercontent.com/paperclipai/paperclip/d1b9448b57a8cfb0e8dbede9bbbc8874a9f66ad7/doc/assets/logos/claude.svg" width="32" alt="Claude"><br><sub>Claude Code</sub></td>
    <td align="center"><img src="https://raw.githubusercontent.com/paperclipai/paperclip/d1b9448b57a8cfb0e8dbede9bbbc8874a9f66ad7/doc/assets/logos/codex.svg" width="32" alt="Codex"><br><sub>Codex</sub></td>
    <td align="center"><img src="https://raw.githubusercontent.com/paperclipai/paperclip/d1b9448b57a8cfb0e8dbede9bbbc8874a9f66ad7/doc/assets/logos/bash.svg" width="32" alt="Bash"><br><sub>Bash</sub></td>
    <td align="center"><img src="https://cdn.simpleicons.org/githubcopilot/8957E5" width="32" alt="GitHub Copilot"><br><sub>GitHub Copilot CLI</sub></td>
  </tr>
</table>
</div>

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

### GitHub Copilot CLI

```bash
copilot plugin marketplace add h0ngcha0/hubo
copilot plugin install hubo@hubo
```

Start a new Copilot CLI session after installing.

### OpenClaw

```bash
openclaw plugins install hubo --marketplace h0ngcha0/hubo
```

Restart the OpenClaw gateway after installing.

### skills.sh

Install Hubo directly into any supported host:

```bash
npx skills add h0ngcha0/hubo --skill hubo -g
```

## Usage

- **Claude Code:** `/hubo implement the change...` when the short alias is unambiguous, or `/hubo:hubo implement the change...`.
- **Codex CLI:** invoke `$hubo:hubo`, then provide the task.
- **ChatGPT or Codex desktop:** type `@`, select **Hubo**, then provide the task.
- **GitHub Copilot CLI:** `/hubo:hubo implement the change...`.
- **OpenClaw:** `/hubo implement the change...`.

For example:

```text
/hubo implement optimistic updates for the cart
```

## License

[MIT](LICENSE) © 2026 h0ngcha0
