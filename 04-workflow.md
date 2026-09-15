# 04 — Workflow, tools, and extending

**English** · [Português](pt-br/04-workflow.md)

Three parts: the loop you run daily, connecting external tools, and extending Claude Code once the
basics are comfortable.

**Time:** 60 minutes for all three. Part 1 alone is 20.

---

# Part 1 — The daily loop

```text
1. Understand  →  read-only questions, or plan mode
2. Isolate     →  a git branch
3. Work        →  describe the change
4. Verify      →  run tests, read the diff
5. Commit      →  a message you would write yourself
6. Clear       →  /clear before the next task
```

Skipping step 1 wastes tokens. Skipping step 4 is how bugs ship.

## 1. Understand first

```text
how does authentication work in this project?
```

```text
list every place that reads the config file
```

For anything non-trivial, use plan mode so the exploration ends with an agreed approach:

```text
/plan migrate the settings loader from JSON to TOML
```

## 2. Isolate

```bash
git checkout -b feature/rate-limiting
```

## 3. Prompt precisely

| Weak | Strong |
|---|---|
| "fix the bug" | "fix the bug where login shows a blank screen after a wrong password" |
| "add validation" | "add email validation to the signup form; reject addresses without `@`; return a 422" |
| "refactor this" | "extract the retry logic from `client.py` into `retry.py` with the same public API; keep tests passing" |

Patterns that work:

- **Numbered steps** for multi-part work: `1. add a column 2. set it on save 3. expose it in the API`
- **Point at examples:** `follow the same pattern as src/routes/invoices.py`
- **Constrain the blast radius:** `only change files under src/api/`
- **Ask for a plan first:** `tell me your plan, then wait for my approval`

When it goes wrong: `Esc` to stop, `/rewind` to undo, `/clear` and restate if it is going in
circles.

## 4. Verify

Never accept a change you have not verified.

```text
/diff
```

```text
run the tests
```

```text
run `make lint` and fix anything it reports
```

The highest-value pattern in this guide — **reproduce, then fix**:

```text
write a test that reproduces the bug, then fix the bug
```

A failing test first proves the bug exists and proves the fix works.

Then have it review its own work:

```text
review your own changes for bugs and edge cases
```

## 5. Commit

```text
commit my changes with a descriptive message
```

Read the message, then confirm:

```bash
git log -1 --stat
```

## 6. Manage context

Context is your scarcest resource. When it fills, Claude Code compacts — lossy.

| Command | Effect |
|---|---|
| `/context` | Show what occupies the window |
| `/compact` | Compact now, optionally keeping named facts |
| `/clear` | Wipe the conversation, keep the files |
| `/usage` | Plan usage and what drives it |

**Clear between unrelated tasks.** Finished the auth bug? `/clear` before the UI work.

**Compact deliberately**, with guidance:

```text
/compact keep the decisions about the retry strategy and the list of changed files
```

**Prefer specific reads:** `read src/routes/users.py` beats `read the whole src/ folder`.

## Sessions

| Command | Effect |
|---|---|
| `claude -c` | Continue the most recent session here |
| `claude -r` | Pick a previous session |
| `/rename` | Name the current session |

## Scripting

```bash
claude -p "explain what src/parser.py does"
git diff main | claude -p "review this diff for bugs"
```

For CI, add `--permission-mode dontAsk --allowedTools "Bash(npm test)" "Read"` so it never waits
for input.

---

# Part 2 — Connect external tools (MCP)

**MCP** lets Claude reach things outside your project: Google Drive, an issue tracker, a database,
a browser. An MCP **server** provides the tools; Claude Code is the client.

**Connect one when you keep copying data into the chat.**

Two words you need:

- **Scope** — `local` (you, this folder), `project` (everyone who clones), `user` (you, everywhere).
- **Transport** — `http` for a hosted service, `stdio` for a program on your machine.

## Practice: a server with no account

```bash
claude mcp add --transport http claude-code-docs https://code.claude.com/docs/mcp
claude mcp list
```

You want `✔ Connected`. Then, in a session:

```text
Use the claude-code-docs server to look up what MCP_TIMEOUT does
```

Remove it when done: `claude mcp remove claude-code-docs`.

## Google Drive — the recommended way

No Google Cloud project, no credentials to manage.

> **Requires** being signed in with a claude.ai account. It does not work with an API key. Check
> with `/status`.

1. Open <https://claude.ai/customize/connectors> and add **Google Drive**.
2. Complete the Google sign-in there.
3. Start Claude Code and check `/mcp` — Google Drive appears.
4. Test read first: `list my 5 most recently modified files in Google Drive`
5. Test write: `create a Google Doc called "Test" with one sentence`

### Optional: the self-hosted way

Skip this unless you specifically want your own Google Cloud project instead of the managed
connector.

1. Install Node:

**macOS**

```bash
brew install node
```

**Windows**

```powershell
winget install OpenJS.NodeJS
```

2. In the [Google Cloud Console](https://console.cloud.google.com/), enable the Drive, Docs,
   Sheets, Slides, and Calendar APIs, then create an OAuth client of type **Desktop app**.
3. Save the downloaded JSON as `~/.config/google-drive-mcp/gcp-oauth.keys.json`. On Windows there is
   no `~/.config` by default — save it as `%APPDATA%\google-drive-mcp\gcp-oauth.keys.json` and use
   that path in step 5.
4. Authorize once:

```bash
npx -y @piotr-agier/google-drive-mcp auth
```

5. Register the server:

**macOS**

```bash
claude mcp add --scope user \
  --env GOOGLE_DRIVE_OAUTH_CREDENTIALS="$HOME/.config/google-drive-mcp/gcp-oauth.keys.json" \
  google-drive -- npx -y @piotr-agier/google-drive-mcp
```

**Windows**

```powershell
claude mcp add --scope user `
  --env GOOGLE_DRIVE_OAUTH_CREDENTIALS="$env:APPDATA\google-drive-mcp\gcp-oauth.keys.json" `
  google-drive -- npx -y @piotr-agier/google-drive-mcp
```

The `--` separates Claude Code's own options from the command it runs. Everything after it is
passed to the server untouched — without it, Claude Code tries to read `-y` as its own flag. This
holds in both shells; only the line continuation differs — `\` in bash, a trailing backtick in
PowerShell.

## Managing servers

```bash
claude mcp list                  # what is configured, and health
claude mcp get <name>            # details
claude mcp remove <name>         # remove
```

Inside a session, `/mcp` shows every server and lets you authenticate or turn one off.

## Security, briefly

MCP servers **run code on your machine**, and their output enters Claude's context — so a server
that fetches web content can carry a prompt injection. Connect servers you trust. Review the tool
count in `/mcp`: a server with 100+ tools is convenient and expensive.

---

# Part 3 — Extending Claude Code

> **Optional.** Come back when you notice yourself repeating the same instructions.

Four mechanisms. Reach for the simplest that works.

| You want | Use |
|---|---|
| A reusable procedure | **Skill** |
| Standing project knowledge | `CLAUDE.md` (chapter 3) |
| A specialist that works in its own context | **Subagent** |
| Something to happen automatically on an event | **Hook** |
| To share all of the above as one unit | **Plugin** |

## Skills

A folder with a `SKILL.md`. Claude finds it by description; you can also invoke it with `/name`.

`~/.claude/skills/release/SKILL.md`:

```markdown
---
name: release
description: Cut a release. Use when the user says "release", "tag a version", or "publish".
---

1. Confirm the working tree is clean. If not, stop and report.
2. Read the current version from `pyproject.toml`.
3. Ask which part to bump: major, minor, or patch. Do not guess.
4. Update `pyproject.toml` and `CHANGELOG.md`.
5. Run `make test`. If it fails, stop.
6. Commit as `chore(release): vX.Y.Z` and tag it.
7. Report the tag. Do not push unless asked.
```

The `description` field is what Claude matches on — it must say *when* to use the skill.

## Subagents

A separate Claude with its own context window. Good for broad searches and repeated reviews that
would otherwise flood your conversation.

`~/.claude/agents/security-reviewer.md`:

```markdown
---
name: security-reviewer
description: Review code for security issues. Use after touching auth, input handling, or secrets.
tools: Read, Grep, Glob, Bash
model: sonnet
---

You review code for security defects. You do not fix them; you report.

Focus on injection, authn/authz, secret handling, input validation, dependency risk.

Report as a list: file:line, severity, what is wrong, one-line fix. If you find nothing, say so.
```

Use it with `use the security-reviewer subagent on the changes in this branch`.

> Subagents cannot ask you questions mid-task. Give them a complete brief.

## Hooks

Shell commands run at lifecycle events. Deterministic — they run whether or not the model decides
to.

This one notifies you when Claude needs input. In `~/.claude/settings.json`:

**macOS**

```json
{
  "hooks": {
    "Notification": [
      {
        "hooks": [
          { "type": "command", "command": "osascript -e 'display notification \"Claude needs you\" with title \"Claude Code\"'" }
        ]
      }
    ]
  }
}
```

**Windows**

```json
{
  "hooks": {
    "Notification": [
      {
        "hooks": [
          { "type": "command", "command": "powershell -NoProfile -Command '[console]::beep(880,300)'" }
        ]
      }
    ]
  }
}
```

`osascript` does not exist on Windows, so the Windows hook beeps instead. A real toast needs the
optional `BurntToast` module: `Install-Module BurntToast`, then
`New-BurntToastNotification -Text "Claude needs you"` as the command.

And this one formats every file Claude edits:

**macOS**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "python3 -c \"import json,sys; print(json.load(sys.stdin)['tool_input']['file_path'])\" | xargs -r npx prettier --write"
          }
        ]
      }
    ]
  }
}
```

**Windows**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "powershell -NoProfile -Command '$j = [Console]::In.ReadToEnd() | ConvertFrom-Json; npx prettier --write $j.tool_input.file_path'"
          }
        ]
      }
    ]
  }
}
```

Both commands read the edited file's path from the hook's JSON input and run `prettier` on it. The
macOS one uses `python3`, which you already have; the Windows one uses PowerShell, which Windows
ships. Neither needs `jq`, which you would have to install. Both need Node from Part 2 for `npx`.

> On Windows the hook shell is `bash` when Git Bash is installed, PowerShell when it is not. A hook
> whose matcher names only `Bash` never fires where only PowerShell exists — silently. Match both
> (`Bash|PowerShell`) when the hook has to run either way.

Debug hooks with `/hooks` — a silently failing hook is the most common problem.

## Plugins

A bundle of the above, installable as a unit.

```text
/plugin marketplace add anthropics/claude-plugins-official
/plugin install mcp-server-dev@claude-plugins-official
```

`/plugin` opens the manager.

---

## Checklist

- [ ] Ran a task through the full loop, including `git diff` before commit
- [ ] Used plan mode at least once
- [ ] Connected one MCP server, and used it
- [ ] Wrote one skill or one hook

Next: **[05 — Reference](05-reference.md)**