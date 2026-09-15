# Claude Code — a beginner's guide

**English** · [Português](pt-br/README.md)

A hands-on path from zero to productive with [Claude Code](https://code.claude.com/docs) on macOS
or Windows: install it, understand its permissions, configure it well, and use it on a real project.

Written for people who **already use AI tools and have opened a terminal**, but would not call
themselves advanced in IT. No programming knowledge assumed.

**Unofficial.** An independent guide through Anthropic's public documentation — see
[`NOTICE.md`](NOTICE.md).

---

## Who this is for

**Yes, if you:** use ChatGPT/Claude/etc. already · are comfortable typing commands · use macOS or
Windows · want to use an AI agent on real code without breaking things.

**No, if you:** have never opened a terminal (read the
[terminal guide](https://code.claude.com/docs/en/terminal-guide) first) · are looking for an API
reference (use the [official docs](https://code.claude.com/docs)) · want a survey of every option
(this guide picks one way per task, on purpose).

---

## What you get

Six short files. Chapters 1–4 are the path; chapter 5 is a reference.

| # | Chapter | Read it when |
|---|---|---|
| 1 | [01-setup.md](01-setup.md) | Now — prerequisites, install, login |
| 2 | [02-first-session.md](02-first-session.md) | Right after — permissions, safety, undo |
| 3 | [03-configuration.md](03-configuration.md) | After your first session. `CLAUDE.md` matters most |
| 4 | [04-workflow.md](04-workflow.md) | Once comfortable — daily loop, Git, external tools |
| 5 | [05-reference.md](05-reference.md) | Look things up. **Read its first section before you need it** |

Chapter 5 opens with recovery, because the fastest way to stop being afraid of an agent that edits
your files is knowing how to undo it.

---

## The 5-minute version

**macOS**

```bash
curl -fsSL https://claude.ai/install.sh | bash    # install
```

**Windows**

```powershell
irm https://claude.ai/install.ps1 | iex           # install
```

```bash
claude --version                                  # confirm
```

```bash
cd ~/Projects/my-project                          # start
claude
```

Log in through the browser when asked. Then:

```text
what does this project do?
```

That is the whole loop. The guide is about doing it *well* and *safely*.

**Requirements:** macOS 13+, or Windows 10 (1809+) / Server 2019+ on x64 or ARM64 — 32-bit is not
supported · a Claude **Pro, Max, Team, or Enterprise** account (the free plan does not include
Claude Code) · a terminal.

---

## Three ideas that explain the rest

**1. It runs in a loop.** Claude Code does not answer once and stop. It reads, acts, observes, and
repeats until the task is done. A vague request wanders; a precise one lands.

**2. Tools have permissions.** It can read files, write files, run shell commands, fetch web pages.
Every call is subject to a permission mode you control. Chapter 2 covers this properly — it is the
single most important chapter.

**3. Context is the scarce resource.** The conversation has a fixed budget. When it fills, Claude
Code compacts — lossily. Chapter 4 teaches habits that keep sessions lean.

---

## How current is this?

| | |
|---|---|
| **Written against** | Claude Code `2.1.x` |
| **Last reviewed** | 2026-09-15 |
| **Cadence** | Claude Code ships weekly |

Claude Code moves fast. Treat the review date as the expiry of the *detail*, not of the approach:
permission modes and the daily loop change slowly, command flags and settings keys change quickly.

**Where the guide and the official docs disagree, the docs win.** If you find drift, an issue with
your `claude --version` is enough to fix it — see [`CONTRIBUTING.md`](CONTRIBUTING.md).

What drifts fastest, if you want to spot-check: `claude --version` against the guide's version ·
`/help` against chapter 5 · permission-mode names against
<https://code.claude.com/docs/en/permission-modes>.

---

## Conventions

- `bash` blocks are typed in your terminal. `text` blocks are typed inside Claude Code. `json`
  blocks are file contents you create.
- Blocks labeled **macOS** or **Windows** are for that system only. Blocks without a label work on
  both — a `powershell` block appears only where the command differs.
- `~` means your home folder (`/Users/yourname`).
- **One recommended way per task.** Where several approaches exist, the guide picks one so you are
  not choosing between things you cannot yet compare.
- Blockquotes marked **Optional** can be skipped on a first pass.

---

## Official documentation

| Topic | URL |
|---|---|
| Docs home | <https://code.claude.com/docs> |
| Quickstart | <https://code.claude.com/docs/en/quickstart> |
| Best practices | <https://code.claude.com/docs/en/best-practices> |
| Common workflows | <https://code.claude.com/docs/en/common-workflows> |
| Settings reference | <https://code.claude.com/docs/en/settings-reference> |
| Permissions | <https://code.claude.com/docs/en/permissions> |
| Permission modes | <https://code.claude.com/docs/en/permission-modes> |
| MCP | <https://code.claude.com/docs/en/mcp> |
| Skills | <https://code.claude.com/docs/en/skills> |
| Subagents | <https://code.claude.com/docs/en/sub-agents> |
| Hooks | <https://code.claude.com/docs/en/hooks> |
| Plugins | <https://code.claude.com/docs/en/plugins> |
| Error reference | <https://code.claude.com/docs/en/errors> |
| Changelog | <https://code.claude.com/docs/en/changelog> |

---

## License

Text licensed under [CC BY 4.0](LICENSE) — share and adapt freely, including commercially, with
attribution.

Unofficial and provided as-is. **Claude** and **Claude Code** are trademarks of Anthropic PBC.
Full attribution and trademark details in [`NOTICE.md`](NOTICE.md).

---

Next: **[01 — Setup](01-setup.md)**