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
| Windows 10 (1809) or later, or Windows Server 2019 or later | Claude Code needs it |
| A 64-bit machine (x64 or ARM64) | 32-bit is not supported |
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

All six also work in PowerShell — `ls`, `cat`, `mkdir`, `cd`, and `pwd` are aliases there. Two
catches: `mkdir -p` errors if the folder already exists, where Unix stays quiet, and chaining uses
`;` where Unix uses `&&`.

`Tab` completes what you are typing. `↑` brings back your last command. `pwd` tells you where you
are if you get lost.

### `~` means your home folder

`~` **is** your home folder: `/Users/yourname` on macOS, `%USERPROFILE%` on Windows (for example
`C:\Users\yourname`). So `~/.claude/settings.json` is `/Users/yourname/.claude/settings.json` on
macOS and `%USERPROFILE%\.claude\settings.json` on Windows.

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

**macOS**

```bash
xcode-select --install
```

A dialog appears. Click **Install** and wait.

**Windows**

```powershell
winget install Git.Git
```

Optional on Windows: install it only if you want Claude Code's Bash tool. Without it, Claude Code
runs commands through PowerShell instead.

Then, on both systems:

```bash
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

> On macOS, if you ran a `git` command before and saw a popup, that was this. Let it finish.

### A package manager — to install one tool later

macOS uses **Homebrew**. Windows already ships **winget**. You need one of them for a tool that a
later chapter installs.

**macOS**

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

It asks for your Mac password — you will not see characters as you type. When it finishes it prints
two or three lines ending in `>> ~/.zprofile`. **Run those lines** — they put Homebrew on your
`PATH`.

**Windows**

```powershell
winget --version
```

Nothing to install: `winget` ships with Windows. If this prints nothing, install **App Installer**
from the Microsoft Store.

Then, on macOS, check Homebrew answers:

```bash
brew --version
```

> **`PATH`** is the list of folders your shell searches for commands. Entries are separated by `:`
> on macOS and `;` on Windows. A tool that is not on `PATH` gives `command not found` (`not
> recognized` on Windows) even when installed.

### A projects folder

```bash
mkdir -p ~/Projects
```

Keep every project inside `~/Projects` so later commands stay predictable. On Windows the same
folder is `%USERPROFILE%\Projects`.

---

## 4. Install

**macOS**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows**

```powershell
irm https://claude.ai/install.ps1 | iex
```

That is the whole install. It downloads the binary, stores versioned files in
`~/.local/share/claude/versions/` (`%USERPROFILE%\.local\share\claude\versions\` on Windows), and
creates a launcher at `~/.local/bin/claude` (`%USERPROFILE%\.local\bin\claude.exe` on Windows).

**It keeps itself updated.** You never run an upgrade command.

> On Windows, run the PowerShell line above. In CMD, use
> `curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd`.

> This guide uses the native installer only. Instructions elsewhere may mention `npm`, Homebrew, or
> `winget` — those also work, but you do not need them.

---

## 5. Verify

```bash
claude --version
```

Expected: something like `2.1.271 (Claude Code)`.

### If you get `command not found`

On Windows the message is `'claude' is not recognized as the name of a cmdlet...`. Either way it is
a `PATH` problem, not a broken install. Confirm the launcher exists:

**macOS**

```bash
ls -l ~/.local/bin/claude
```

**Windows**

```powershell
Test-Path "$env:USERPROFILE\.local\bin\claude.exe"
```

If it exists, add the folder to your `PATH`:

**macOS**

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

**Windows**

```powershell
[Environment]::SetEnvironmentVariable('PATH', "$([Environment]::GetEnvironmentVariable('PATH','User'));$env:USERPROFILE\.local\bin", 'User')
```

Windows has no rc file: that writes a user variable in the registry, so reopen the terminal for it
to take effect.

Retry. Open a new terminal window if it still fails.

If the check says the file is missing, the install did not finish — run it again and read the output.

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

Credentials go into the macOS Keychain, and on Windows into
`%USERPROFILE%\.claude\.credentials.json`, protected by your user profile's permissions. Both
refresh automatically. To switch accounts later, run `/login` inside a session.

---

## 7. Where things live

| macOS | Windows | Holds |
|---|---|---|
| `~/.local/bin/claude` | `%USERPROFILE%\.local\bin\claude.exe` | The launcher |
| `~/.claude/settings.json` | `%USERPROFILE%\.claude\settings.json` | Your settings — you create this in chapter 3 |
| `~/.claude/CLAUDE.md` | `%USERPROFILE%\.claude\CLAUDE.md` | Instructions for every project |
| `~/.claude.json` | `%USERPROFILE%\.claude.json` | Claude Code's own state file |
| `<project>/CLAUDE.md` | `<project>/CLAUDE.md` | Instructions for one project |
| `<project>/.claude/settings.json` | `<project>/.claude/settings.json` | Settings for one project |

---

## 8. Updates and uninstall

**Updates:** automatic, in the background, applied at next start. Force one with `claude update`.

For fewer surprises, use the `stable` channel — about a week behind, skips regressions. In
`~/.claude/settings.json` (`%USERPROFILE%\.claude\settings.json` on Windows):

```json
{ "autoUpdatesChannel": "stable" }
```

**Uninstall** — remove the program:

**macOS**

```bash
rm -f ~/.local/bin/claude
rm -rf ~/.local/share/claude
```

**Windows**

```powershell
Remove-Item -Force "$env:USERPROFILE\.local\bin\claude.exe"
Remove-Item -Recurse -Force "$env:USERPROFILE\.local\share\claude"
```

Add this to also erase settings and history:

**macOS**

```bash
rm -rf ~/.claude
rm -f ~/.claude.json
```

**Windows**

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.claude"
Remove-Item -Force "$env:USERPROFILE\.claude.json"
```

---

## Checklist

- [ ] `git --version` works
- [ ] Your package manager answers: `brew --version` (macOS) or `winget --version` (Windows)
- [ ] `~/Projects` exists (`%USERPROFILE%\Projects` on Windows)
- [ ] `claude --version` prints a version
- [ ] `claude doctor` reports no blocking problems
- [ ] Logged in, and `/status` shows the right account

Next: **[02 — Your first session](02-first-session.md)**