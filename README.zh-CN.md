<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.png">
    <img src="assets/logo.png" width="220" alt="Hubo，双手过招的老顽童">
  </picture>
</p>

<h1 align="center">Hubo</h1>

<p align="center">
  <strong>双手互搏</strong><br>
  <em>双手过招，打磨每次改动。只有真正需要你拍板的问题，才会交到你手上。</em>
</p>

<p align="center">
  <sub>这是简体中文翻译。最新、权威版本请参阅<a href="README.md">英文 README</a>。</sub>
</p>

---

[周伯通](https://zh.wikipedia.org/wiki/%E5%91%A8%E4%BC%AF%E9%80%9A)是武侠世界里最迷人的怪杰之一：武功盖世，心性却像个顽童。普通练功变得太无聊时，他发明了一门荒诞又绝妙的功夫——**双手互搏**。

他让两只手独立行动。一手进攻，另一手应对；两手各使一门武功。同一个人，成了自己的两个对手。它们不断拆招，直到谁也无法再让另一只手措手不及。

听起来像个把戏，其实是一套纪律：把创造和批评分开，让双方独立思考，用分歧暴露任何一方独自工作时会漏掉的问题。

Hubo 把这场小小的对决放进你的编程智能体。

Hubo 取自**互搏**（*hùbó*）：彼此过招。不是两个智能体客气地轮流发言，也不是评审者站在门口说一句“看起来不错”。双手过招，打磨每次改动。只有真正需要你拍板的问题，才会交到你手上。它们交换证据，直到代码本身——而不是任何一个智能体的自尊心——胜出。

<div align="center">
<table>
  <tr>
    <td align="center"><strong>支持</strong></td>
    <td align="center"><img src="https://raw.githubusercontent.com/paperclipai/paperclip/d1b9448b57a8cfb0e8dbede9bbbc8874a9f66ad7/doc/assets/logos/openclaw.svg" width="32" alt="OpenClaw"><br><sub>OpenClaw</sub></td>
    <td align="center"><img src="https://raw.githubusercontent.com/paperclipai/paperclip/d1b9448b57a8cfb0e8dbede9bbbc8874a9f66ad7/doc/assets/logos/claude.svg" width="32" alt="Claude"><br><sub>Claude Code</sub></td>
    <td align="center"><img src="https://raw.githubusercontent.com/paperclipai/paperclip/d1b9448b57a8cfb0e8dbede9bbbc8874a9f66ad7/doc/assets/logos/codex.svg" width="32" alt="Codex"><br><sub>Codex</sub></td>
    <td align="center"><img src="https://cdn.simpleicons.org/githubcopilot/8957E5" width="32" alt="GitHub Copilot"><br><sub>GitHub Copilot CLI</sub></td>
  </tr>
</table>
</div>

## 前后对比

没有 Hubo 时，一个智能体会实现功能、跑测试、复查自己的推理，然后把结果交给你。作者、测试者和评审者共享同样的盲区。

使用 Hubo：

```text
你的任务
   ↓
工作智能体 ── 实现 + 证据 ──▶ 评审智能体
     ▲                         │
     └──── 修复或有据可依的反驳 ───┘
                    反复
                     ↓
            达成共识的结果 —— 或交给你
```

只有在每个具体问题都得到解决后，工作才会交到你手上；除非剩下的问题确实需要你的判断。

## 工作原理

Hubo 在同一段对话中保持两个持续存在的角色：

| 手 | 职责 |
|---|---|
| **工作智能体** | 理解任务、修改代码、运行测试，并以修复或基于证据的反驳回答每一条评审意见。 |
| **评审智能体** | 保持独立且只读，检查实际 diff 和验证证据，并返回带编号、可执行的问题。 |
| **协调者** | 在二者之间传递消息，跨轮次保留未解决问题，并且只在真正满足退出条件时停止。 |

循环会在以下情况之一发生时结束：

- 评审智能体放行代码，所有问题都已达成一致；或
- 两个智能体发现了只有你才能决定的产品、需求或技术方向问题。

评审智能体可以派出专家，工作智能体也可以委派实现任务。但最上层的结构始终不变：一只手创造，另一只手检验创造的结果。

Hubo 只在显式调用时运行。普通的编程请求仍然只是普通请求。

## 安装

### Claude Code

分别发送以下两个命令：

```text
/plugin marketplace add h0ngcha0/hubo
```

```text
/plugin install hubo@hubo
```

然后运行 `/reload-plugins`，或开始一段新对话。

### Codex

```bash
codex plugin marketplace add h0ngcha0/hubo
codex plugin add hubo@hubo
```

重启 Codex 并开始一段新对话，让它发现这个技能。

### GitHub Copilot CLI

```bash
copilot plugin marketplace add h0ngcha0/hubo
copilot plugin install hubo@hubo
```

安装后开始一个新的 Copilot CLI 会话。

### OpenClaw

```bash
openclaw plugins install hubo --marketplace h0ngcha0/hubo
```

安装后重启 OpenClaw gateway。

### skills.sh

把 Hubo 直接安装到任一受支持的宿主：

```bash
npx skills add h0ngcha0/hubo --skill hubo -g
```

## 使用

- **Claude Code：** 短名称没有歧义时使用 `/hubo 实现这个改动...`，否则使用 `/hubo:hubo 实现这个改动...`。
- **Codex CLI：** 调用 `$hubo:hubo`，然后提供任务。
- **ChatGPT 或 Codex 桌面应用：** 输入 `@`，选择 **Hubo**，然后提供任务。
- **GitHub Copilot CLI：** 使用 `/hubo:hubo 实现这个改动...`。
- **OpenClaw：** 使用 `/hubo 实现这个改动...`。

例如：

```text
/hubo 为购物车实现乐观更新
```

## 要求

Hubo 需要宿主能够创建并恢复两个可寻址的智能体，并把两个智能体的结果都返回给协调者。目前这个仓库为 Claude Code、Codex、GitHub Copilot CLI 和 OpenClaw 提供适配。

工作流会保留工作树中已有的改动，确保评审智能体只读，接受有理有据的反驳，并优先修复根因，而不是只消除表面症状。

## 许可证

[MIT](LICENSE) © 2026 h0ngcha0
