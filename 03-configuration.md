# 03 — Configuration

**English** · [Português](pt-br/03-configuration.md)

Configuration is two different things:

1. **Settings** (`settings.json`) — mechanical behaviour: permissions, model, theme.
2. **Instructions** (`CLAUDE.md`) — what Claude should *know* about your project.

The second matters far more for output quality. Most of this chapter is about it.

**Time:** 25 minutes.

---

## Part 1 — `CLAUDE.md`

A Markdown file of standing instructions, loaded automatically at the start of every session.

### Let Claude write the first draft

```text
/init
```

This analyses your project and generates a `CLAUDE.md`. **Then edit it** — `/init` produces a
reasonable draft, not a finished file.

### Where it lives

| Path | Applies to |
|---|---|
| `~/.claude/CLAUDE.md` | You, every project |
| `<project>/CLAUDE.md` | Everyone in the project — **commit this** |

### What belongs in it

The test: *would a new teammate need this on day one?*

**Include:** exact build/test/lint/run commands · stack and versions · directory layout for
anything not self-evident · conventions (naming, error handling, commit style) · gotchas ("the
`legacy/` folder is frozen") · what not to touch.

**Exclude:** anything derivable by reading the code · long tutorials · secrets · aspirational
rules nobody follows.

### A real example

```markdown
# Project: acme-api

## Stack
- Python 3.12, FastAPI, SQLAlchemy 2.x, PostgreSQL 16
- Tests: pytest + pytest-asyncio. Run with `make test`.
- Lint: ruff. Run `make lint` before committing.

## Commands
- `make dev` — start the dev server on :8000
- `make test` — full suite (needs Postgres running)
- `make migrate` — apply migrations

## Layout
- `src/api/routes/` — HTTP handlers, one module per resource
- `src/api/services/` — business logic; routes call services, never the ORM directly
- `tests/` — mirrors `src/`

## Conventions
- All handlers are `async def`. No sync DB calls.
- Errors: raise `AppError` subclasses.
- Commit messages: Conventional Commits (`feat:`, `fix:`, `chore:`).

## Gotchas
- `src/api/legacy/` is frozen — do not refactor it.
- `make test` truncates the local DB. Never point it at staging.
- Migrations must be reversible: always write `downgrade()`.
```

### Keep it short

`CLAUDE.md` costs context on **every** message. Aim for 50–150 lines. If it grows past that, split
it and reference the rest.

### Two things to know

**Edits do not apply to the running session.** It is read once at startup. Restart, or `/clear`
after editing.

**Verify it loaded** with `/context`.

---

## Part 2 — Settings

### Where settings live

| Scope | File | Affects |
|---|---|---|
| **User** | `~/.claude/settings.json` | You, every project |
| **Shared project** | `<project>/.claude/settings.json` | Everyone — **commit this** |
| **Project local** | `<project>/.claude/settings.local.json` | You, this project — do not commit |

If the same key appears in two files, the higher wins:
`project local > shared project > user`.

**Lists merge instead of overriding.** If your user file allows `Bash(npm test)` and the project
file allows `Bash(npm run lint)`, both apply.

### A safe starting user file

Create `~/.claude/settings.json`:

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": ["Read", "Bash(git status)", "Bash(git diff *)"],
    "deny": ["Read(./.env)", "Read(./.env.*)"]
  }
}
```

The `$schema` line gives you autocomplete in VS Code and other editors.

> Settings files are strict JSON: no comments, no trailing comma. A trailing comma produces a
> `Settings Error` at the next start.

### Permission rules

Rules look like `Tool(pattern)`, in three lists:

| List | Meaning |
|---|---|
| `allow` | Run without asking |
| `ask` | Always prompt, even in Auto mode |
| `deny` | Never run — **wins over every mode** |

`ask` rules are how you keep a human checkpoint in Auto mode:

```json
{
  "permissions": {
    "ask": ["Bash(git push *)"]
  }
}
```

That is the classic one: let Claude do everything, but always confirm before code leaves your
machine.

> Rules match the command **as written**. `git push` and `git -C /path push` are different
> strings.

### Verify and change settings

```text
/status
```

The `Setting sources` line lists the files that actually loaded. If yours is not there, it did not
load — usually a wrong path or invalid JSON.

```text
/config
```

Opens a menu for common options and writes them for you.

For validation errors, run `claude doctor` from your shell.

---

## Part 3 — Model

```text
/model
```

Saves your choice as the default for new sessions.

**Effort level** controls how hard it thinks — higher costs more and is worth it for hard
debugging, not typos:

```text
/effort
```

To set a project default, in `<project>/.claude/settings.json`:

```json
{ "model": "claude-sonnet-5" }
```

> Switching models mid-session invalidates the prompt cache, so the next turn re-reads the whole
> conversation uncached. Pick one and stay on it.

---

## Checklist

- [ ] `<project>/CLAUDE.md` exists, is under ~150 lines, and has real commands
- [ ] `/context` shows it loaded
- [ ] `~/.claude/settings.json` exists with a `$schema` line
- [ ] `deny` rules protect your `.env` files
- [ ] An `ask` rule guards `git push`
- [ ] `/status` lists the settings files you expect
- [ ] `claude doctor` reports no validation errors

Next: **[04 — Workflow](04-workflow.md)**