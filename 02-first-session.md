# 02 — Your first session

**English** · [Português](pt-br/02-first-session.md)

This chapter gets you through a real session and teaches the one thing that separates pleasant
Claude Code use from painful use: **permission modes.**

**Time:** 20 minutes.

---

## 1. Start

```bash
cd ~/Projects/your-project
claude
```

> **Always start Claude Code from the project folder you want it to work on.** That folder becomes
> the session's working directory. Files inside are treated as yours; files outside are external.

No project yet? Make one:

```bash
mkdir -p ~/Projects/hello-claude && cd ~/Projects/hello-claude && git init
claude
```

---

## 2. Ask before changing anything

Read-only exploration is the safest first move:

```text
what does this project do?
```

```text
explain the folder structure
```

Notice you did not have to name the files. Claude explores on its own.

---

## 3. Permission modes

This is the core safety concept. A permission mode decides what Claude may do without asking.

Press **`Shift+Tab`** to cycle modes. The status bar shows the active one. There are **four you
need**, exactly the four `Shift+Tab` cycles through:

| Mode | Runs without asking | Use for |
|---|---|---|
| **Manual** | Reads only | Sensitive work, unfamiliar code, reviewing everything |
| **Accept edits** | Reads, file edits, common filesystem commands | Iterating on code you will review afterwards |
| **Plan** | Reads, plus approved commands — **no edits** | Exploring before changing |
| **Auto** | Everything, with a safety classifier in front | Long tasks, fewer prompts |

> **Optional.** Two more modes exist (`dontAsk`, `bypassPermissions`). They need a command-line
> flag, so you can ignore them. Chapter 5 lists them.

**Your session starts in Auto** on Pro, Max, and Team plans, and in Manual on Enterprise or with
a Console API key. To force a mode:

```bash
claude --permission-mode plan
```

### What Auto actually does

It is not "no checks". A separate classifier model reviews each action and blocks things that look
risky. By default it blocks `curl | bash`, `git push --force`, `git reset --hard`, `terraform
destroy`, mass cloud deletion, and commits that leak secrets. It allows local file operations,
installing declared dependencies, and pushing to a branch of your own repo.

If it blocks something three times in a row, Auto pauses and starts asking you again.

See the full lists with:

```bash
claude auto-mode defaults
```

### Two things no mode auto-approves

**Protected paths** — `.git/`, `.claude/`, `.vscode/`, `.idea/`, and files like `.zshrc`,
`.gitconfig`, `.mcp.json`.

**Critical paths** — `rm` on your home folder, `/`, top-level directories like `/usr`, and your
working directory. A circuit breaker against a bad glob.

---

## 4. Make a change

Press `Shift+Tab` until you see `accept edits on`, then:

```text
create hello.py with a function that prints a greeting
```

Read what it wrote (`cat hello.py`), then:

```text
run hello.py
```

---

## 5. Interrupt and undo

| Action | How |
|---|---|
| **Interrupt what Claude is doing** | `Esc` |
| Undo — files *and* conversation | `/rewind` |
| See what changed | `/diff` |
| Discard all uncommitted changes | `git checkout -- .` |
| Exit | `Ctrl+D` twice |

**`Esc` is the most important key in Claude Code.** If it is doing something unexpected, press it.
Nothing is lost — you redirect and continue.

`/rewind` is the built-in undo and works even for changes you already approved.

> **If you get lost:** `Esc`, then `/rewind`. That fixes almost everything.

---

## 6. Three habits

### Work in Git

Git makes everything reversible and reviewable.

```bash
git add -A && git commit -m "checkpoint before Claude works"
```

Commit before letting Claude do anything substantial. Then `git diff` to review, and
`git checkout -- .` to throw it all away.

### Read the diff before you trust it

Approving an edit is not reading it. Use `/diff` during a session and `git diff` after. If you
cannot explain the change, do not commit it.

### Match the mode to the risk

| Situation | Mode |
|---|---|
| First time in an unfamiliar repo | Manual |
| Reviewing unfamiliar code | Manual |
| Iterating on code you know | Accept edits |
| "I don't know what to change yet" | Plan |
| Long refactor or test-fix loop | Auto |

---

## 7. Plan mode

The most underused feature, and the best fit for anything non-trivial.

```text
/plan add rate limiting to the API
```

Claude researches and presents a plan **without editing**. You then choose to approve it (and
start working) or keep planning. `Ctrl+G` opens the plan in your editor before you approve.

Use it when the task spans several files, or when you want to agree on a design before code
appears.

---

## 8. What NOT to do

- **Do not let it work on uncommitted work you care about.** Commit first.
- **Do not paste secrets into the conversation.** Claude may write them into a file or commit.
- **Do not trust output blindly.** Claude can be confidently wrong.
- **Do not approve edits you have not read.**
- **Do not leave it unattended on a production repo** until you have seen how it behaves.

---

## Checklist

- [ ] Started from the project folder
- [ ] Asked a read-only question first
- [ ] Cycled the modes with `Shift+Tab`
- [ ] Made one small change and read it
- [ ] Tried `Esc`
- [ ] Tried `/rewind`
- [ ] Made a Git commit and inspected it with `git diff`

Next: **[03 — Configuration](03-configuration.md)**