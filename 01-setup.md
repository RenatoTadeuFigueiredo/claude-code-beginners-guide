# 01 — Setup

**English** · [Português](pt-br/01-setup.md)

Prerequisites, install, and login. By the end you have Claude Code running.

**Time:** 25 minutes, most of it waiting for downloads.

If `~`, JSON, and `git` are already familiar, skip to [section 4](#4-install).

---

## 1. What you need

| Requirement | Why |
|---|---|
| macOS 13 (Ventura) or later | Claude Code needs it |
| A Claude **Pro, Max, Team, or Enterprise** account | The free plan does not include Claude Code |
| A terminal | You have used one |

You do not need to program.

---

## 2. Six terminal commands, and what JSON is

You only need these six commands.

| Command | Does |
|---|---|
| `cd <folder>` | Go into a folder |
| `cd ..` | Go up one folder |
| `ls` | List what is here |
| `mkdir -p <path>` | Create a folder |
| `cat <file>` | Print a file |
| `pwd` | Print where you are |

`Tab` completes what you are typing. `↑` brings back your last command. `pwd` tells you where you
are if you get lost.

### `~` means your home folder

`~` **is** `/Users/yourname`. So `~/.claude/settings.json` is
`/Users/yourname/.claude/settings.json`.

### JSON is a text format, with three rules

You will edit JSON in chapter 3.

**Curly braces hold an object.** Keys and values separated by colons, items by commas:

```json
{ "name": "my-project", "version": 2 }
```

**Square brackets hold a list:**

```json
{ "tags": ["api", "internal"] }
```

**Strings need double quotes.** Numbers and `true`/`false` do not.

Two mistakes break JSON every time: a **trailing comma**, and **comments** (JSON has no `//`).

```json
{
  "port": 8000,
  "enabled": true,
}
```

That example is deliberately broken — the comma after `true` must go.

Check any JSON file with:

```bash
python3 -m json.tool ~/.claude/settings.json
```

Silence means valid.

---

## 3. Install three tools

### `git` — how you track and undo changes

```bash
xcode-select --install
```

A dialog appears. Click **Install** and wait. Then:

```bash
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

> If you ran a `git` command before and saw a popup, that was this. Let it finish.

### Homebrew — to install one tool later

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

It asks for your Mac password — you will not see characters as you type. When it finishes, it
prints two or three lines ending in `>> ~/.zprofile`. **Run those lines** (they put Homebrew on
your `PATH`), then check:

```bash
brew --version
```

> **`PATH`** is the list of folders your shell searches for commands. A tool that is not on
> `PATH` gives `command not found` even when installed.

### A projects folder

```bash
mkdir -p ~/Projects
```

Keep every project inside `~/Projects` so later commands stay predictable.

---

## 4. Install

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

That is the whole install. It downloads the binary, stores versioned files in
`~/.local/share/claude/versions/`, and creates a launcher at `~/.local/bin/claude`.

**It keeps itself updated.** You never run an upgrade command.

> This guide uses the native installer only. Instructions elsewhere may mention `npm` or Homebrew
> — those also work, but you do not need them.

---

## 5. Verify

```bash
claude --version
```

Expected: something like `2.1.271 (Claude Code)`.

### If you get `command not found`

A `PATH` problem, not a broken install. Confirm the launcher exists:

```bash
ls -l ~/.local/bin/claude
```

If it exists, add the folder to your `PATH`:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Retry. Open a new terminal window if it still fails.

If `ls` says the file is missing, the install did not finish — run it again and read the output.

### Full diagnostics

```bash
claude doctor
```

Prints install health, settings errors, and suggested fixes without starting a session.

---

## 6. Log in

```bash
cd ~/Projects
claude
```

Your browser opens. Sign in, then return to the terminal.

> If the browser cannot reach `localhost`, Claude Code prints a URL instead. Sign in, copy the
> **full** address from your browser's address bar, and paste it into the terminal.

Confirm the account:

```text
/status
```

Credentials go into the macOS Keychain and refresh automatically. To switch accounts later, run
`/login` inside a session.

---

## 7. Where things live

| Path | Holds |
|---|---|
| `~/.local/bin/claude` | The launcher |
| `~/.claude/settings.json` | Your settings — you create this in chapter 3 |
| `~/.claude/CLAUDE.md` | Instructions for every project |
| `~/.claude.json` | Claude Code's own state file |
| `<project>/CLAUDE.md` | Instructions for one project |
| `<project>/.claude/settings.json` | Settings for one project |

---

## 8. Updates and uninstall

**Updates:** automatic, in the background, applied at next start. Force one with `claude update`.

For fewer surprises, use the `stable` channel — about a week behind, skips regressions. In
`~/.claude/settings.json`:

```json
{ "autoUpdatesChannel": "stable" }
```

**Uninstall** — remove the program:

```bash
rm -f ~/.local/bin/claude && rm -rf ~/.local/share/claude
```

Add `rm -rf ~/.claude && rm -f ~/.claude.json` to also erase settings and history.

---

## Checklist

- [ ] `git --version` works
- [ ] `brew --version` works
- [ ] `~/Projects` exists
- [ ] `claude --version` prints a version
- [ ] `claude doctor` reports no blocking problems
- [ ] Logged in, and `/status` shows the right account

Next: **[02 — Your first session](02-first-session.md)**