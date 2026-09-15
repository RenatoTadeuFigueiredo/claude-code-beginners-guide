# 05 — Reference

Look things up here. **Read the first section once, before you need it.**

---

## Undo and recover

Almost nothing you do with Claude Code is permanent.

| Problem | Fix |
|---|---|
| Claude made a mess | `/rewind` — restores files *and* conversation |
| Claude is still running | `Esc`, then `/rewind` if needed |
| Discard everything since your last commit | `git diff --stat` to check, then `git checkout -- .` |
| Broke `settings.json` | `mv ~/.claude/settings.json{,.broken}` — Claude Code works without it |
| Broke `~/.claude.json` | Restore from `~/.claude/backups/` — copy the newest `.claude.json.backup.*` |
| Broke `CLAUDE.md` | Delete the bad lines, or `/init` to regenerate |
| Claude deleted a committed file | `git checkout HEAD -- path/to/file` |
| Nothing works | `claude doctor`, then `claude --setting-sources ""` to test without settings |
| Beyond repair | Reinstall — settings and history survive |

**Reinstall:**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Find a broken `settings.json` with:

```bash
python3 -m json.tool ~/.claude/settings.json
```

---

## Shell commands

| Command | Effect |
|---|---|
| `claude` | New session here |
| `claude "fix the build"` | New session with a prompt |
| `claude -c` | Continue the most recent session here |
| `claude -r` | Pick a previous session |
| `claude -p "explain this"` | One-off run, then exit |
| `claude --permission-mode <mode>` | Start in a specific mode |
| `claude --model <name>` | Start on a model |
| `claude --add-dir ../shared` | Also grant access to another folder |
| `claude --worktree feature/x` | Run in an isolated git worktree |
| `claude --settings '<json>'` | Override settings for this session |
| `claude --debug-file <path>` | Write a debug log |
| `claude --version` | Version |
| `claude doctor` | Validate install and settings |
| `claude update` | Update now |
| `claude auto-mode defaults` | Print the classifier's allow/block lists |

**Piping works:**

```bash
git diff main | claude -p "review this for bugs"
```

---

## In-session commands

| Command | Effect |
|---|---|
| `/help` | All commands |
| `/clear` | Wipe conversation, keep files |
| `/compact [notes]` | Compact, optionally keeping named facts |
| `/context` | What occupies the context window |
| `/diff` | Changes made this session |
| `/rewind` | Undo — files and conversation |
| `/plan <task>` | Plan without editing |
| `/resume` | Reopen a previous session |
| `/rename` | Name this session |
| `/cd <path>` | Move the session |
| `/status` | Account, model, loaded settings files |
| `/doctor` | Diagnostics |
| `/permissions` | Active rules and recently denied actions |
| `/hooks` | Registered hooks and recent runs |
| `/usage` | Plan usage and what drives it |
| `/cost` | Cost of this session |
| `/mcp` | MCP servers, status, tools |
| `/config` | Personal options menu |
| `/model` | Choose a model (`s` = this session only) |
| `/effort` | Reasoning effort |
| `/init` | Generate a `CLAUDE.md` |
| `/login` | Switch account |
| `/add-dir` | Grant access to another folder |
| `/plugin` | Plugin manager |
| `/code-review` | Review current changes |
| `/feedback` | Report a problem |
| `/exit` | Quit |

---

## Keyboard

| Key | Action |
|---|---|
| `Esc` | **Interrupt** |
| `Shift+Tab` | Cycle permission modes |
| `Ctrl+D` twice | Exit |
| `↑` | Previous prompt |
| `Tab` | Complete a command |
| `Ctrl+G` | Open the plan in your editor |
| `Ctrl+R` | Search command history across projects |
| `/` | Show commands and skills |
| `!` | Prefix a shell command |
| `@` | Reference a file |

---

## Permission modes

The four in the `Shift+Tab` cycle:

| Mode | Runs without asking | Use for |
|---|---|---|
| `default` (Manual) | Reads only | Sensitive work, unfamiliar code |
| `acceptEdits` | Reads, edits, common filesystem commands | Iterating on code you will review |
| `plan` | Reads + approved commands, no edits | Deciding before changing |
| `auto` | Everything, with a safety classifier | Long tasks, fewer prompts |

Reachable only by flag:

| Mode | Behaviour | Use for |
|---|---|---|
| `dontAsk` | Reads and pre-approved only; rest denied | Scripts, CI |
| `bypassPermissions` | Everything, no checks | Containers and VMs only |

**Never auto-approved in any mode:** writes to protected paths (`.git/`, `.claude/`, shell rc
files, `.mcp.json`) and `rm`/`rmdir` on critical paths (home, `/`, top-level dirs, your working
directory).

> `auto` and `bypassPermissions` do **not** work from `.claude/settings.json`. Set them in
> `~/.claude/settings.json`.

---

## Permission rules

```json
{
  "permissions": {
    "allow": ["Bash(npm run test *)", "Read"],
    "ask":   ["Bash(git push *)"],
    "deny":  ["Read(./.env)", "Bash(rm -rf *)"]
  }
}
```

`deny` beats every mode. Rules match the command **as written**, not semantically.

---

## Files and precedence

| Path | Purpose |
|---|---|
| `~/.claude/settings.json` | Your settings |
| `~/.claude/CLAUDE.md` | Instructions for every project |
| `~/.claude/skills/`, `~/.claude/agents/` | Your skills and subagents |
| `~/.claude.json` | State and MCP servers |
| `~/.claude/projects/` | Session transcripts |
| `<project>/CLAUDE.md` | Project instructions — commit |
| `<project>/.claude/settings.json` | Project settings — commit |
| `<project>/.claude/settings.local.json` | Personal project settings — do not commit |
| `<project>/.mcp.json` | Project MCP servers — commit |

Precedence: `managed > flags > project local > shared project > user`. Lists merge; single keys
override.

---

## MCP commands

```bash
claude mcp add --transport http <name> <url>     # hosted
claude mcp add <name> -- npx -y <package>        # local — note the --
claude mcp add --scope user ...                  # all projects
claude mcp list                                  # health status
claude mcp get <name>                            # details and scope
claude mcp remove <name>
claude mcp login <name>                          # OAuth from the shell
```

---

## Prompt patterns

```text
/plan <task>                                     decide before changing
explain <file> assuming I have never seen it      onboard fast
write a test that reproduces <bug>, then fix it   prove before fixing
only change files under <path>                    limit blast radius
follow the same pattern as <file>                 point at an example
run the tests and fix anything that fails         close the loop
review your own changes for edge cases            second pass
```

**Specificity beats politeness.** Name files, constraints, and expected behaviour.

---

## Habits

- Branch before big work: `git checkout -b feature/x`
- Commit before letting it run loose
- Read the diff — `/diff`, then `git diff`
- `Esc` early
- `/clear` between unrelated tasks
- Reproduce, then fix
- Never bypass permissions outside a container

---

## Troubleshooting

Start with `claude doctor` (shell) or `/doctor` (in-session). It reports what is broken.

### Install and login

| Symptom | Fix |
|---|---|
| `claude: command not found` | `PATH` problem. `echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc` |
| Launcher file missing | Reinstall: `curl -fsSL https://claude.ai/install.sh \| bash` |
| Login loop, browser cannot reach localhost | Claude Code prints a URL. Sign in, paste the full callback URL back |
| `/mcp` connectors missing, or "session token rejected" | Connectors need a claude.ai login, not an API key. Check `/status`, then `/login` |

### Permissions and settings

| Symptom | Fix |
|---|---|
| Asks for everything | You are in Manual. `Shift+Tab` |
| Auto mode unavailable | Needs a recent model. Or `disableAutoMode` is set |
| A setting is ignored | `/status` shows what loaded. Check precedence and the right file |
| `Settings Error` at startup | Invalid JSON. `python3 -m json.tool <file>` |
| `deny` rule not blocking | Rules match the command as written. Check `claude --debug` |
| `bypassPermissions` refused | Cannot be enabled in a session started without it |

### Context and performance

| Symptom | Fix |
|---|---|
| High CPU or memory | `/context`, then `/clear` and narrow the request |
| Constant compaction | Session too long. `/clear`, or lower `autoCompactWindow` |
| Responses feel worse | Long compacted context. `/clear` and restart |
| Turn slow after a model switch | Expected — the prompt cache was invalidated |

### MCP

| Symptom | Fix |
|---|---|
| `/mcp` shows nothing | Added at `local` scope from another folder. Re-add or use `--scope user` |
| `Failed to connect` | Run the server command by hand and read the error |
| `Needs authentication` | `/mcp` → select server → Authenticate |
| Connects, zero tools | Missing environment variable — check `--env` |
| `.mcp.json` ignored | Restart the session; it is read at startup |
| Startup timeout | `MCP_TIMEOUT=60000 claude` |

### Sessions

| Symptom | Fix |
|---|---|
| Lost a session | `claude -r`, or `/resume` |
| Wrong directory | `/cd /path/to/project` |
| Claude edits files outside the project | `claude --add-dir ../shared` |

---

## Cost

```text
/usage
/cost
```

Reduce it with `/clear` between tasks, a short `CLAUDE.md`, Sonnet for routine work, lower
`/effort` for mechanical tasks, and fewer MCP servers (each loads its tool list every session).

---

## Getting help

1. `claude doctor`, and read all of it
2. `/status` to confirm what loaded
3. Reproduce in a minimal project
4. `claude --debug-file /tmp/claude-debug.log` and read the tail
5. Search <https://code.claude.com/docs>
6. `/feedback` in a session
7. Community: <https://www.anthropic.com/discord>

When asking, include the version (`claude --version`), macOS version, the exact command, the exact
error, and the relevant settings with secrets redacted.

Error messages are indexed at <https://code.claude.com/docs/en/errors>.

---

## Limits worth remembering

| Limit | Detail |
|---|---|
| Free plan is not enough | Needs Pro, Max, Team, Enterprise, or Console |
| `auto` / `bypassPermissions` in project settings | Ignored — set them in `~/.claude/settings.json` |
| `CLAUDE.md` edits | Do not apply to the running session |
| Model switch | Invalidates the prompt cache |
| Permission rules | Match the command as written |
| MCP servers | Each costs context every session |
| Compaction | Lossy |

---

Back to **[README](README.md)**