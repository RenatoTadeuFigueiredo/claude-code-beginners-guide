# Contributing

Corrections are welcome. This guide's value depends on being accurate, so a fix that keeps it
aligned with the official documentation is worth more than new content.

## What is most useful

| Kind of change | Notes |
|---|---|
| **A command that no longer works** | Highest value. Include your Claude Code version |
| **A step that assumes too much** | This guide targets people who use AI and a terminal but are not IT-advanced. Gaps in that assumption are bugs |
| **A fact that drifted** | Settings keys, permission modes, file paths, flags |
| **A clearer wording** | Only if it shortens, or removes ambiguity |
| **A new chapter** | Justify it — this guide deliberately stays small |

## What to avoid

- **Adding options.** Where two ways exist, the guide picks one. A pull request that adds "or you
  could also…" needs a reason.
- **Restating the official docs.** Link instead of duplicating reference material.
- **Longer prose.** The whole guide is intentionally under ~1,500 lines.

## Style

- English, second person, present tense.
- Short sentences. Tables over paragraphs where the content is enumerable.
- `bash` blocks are typed in a shell, `text` blocks inside a Claude Code session, `json` blocks are
  file contents.
- Anything skippable goes in a blockquote starting with `**Optional**.`
- No emoji.

## Before opening a pull request

1. Verify the claim against <https://code.claude.com/docs> and say so in the description.
2. Run the checks below.
3. Keep one logical change per pull request.

```bash
# every local link resolves, and every fence is balanced
python3 - <<'PY'
from pathlib import Path
import re
FENCE = chr(96) * 3          # three backticks, without quoting them literally
bad = []
for f in sorted(Path('.').rglob('*.md')):
    t = f.read_text()
    if t.count(FENCE) % 2:
        bad.append(f'{f}: unbalanced fence')
    for u in re.findall(r'\[[^\]]*\]\(([^)]+)\)', t):
        if u.startswith(('http', '#', 'mailto:')):
            continue
        if not (f.parent / u.split('#')[0]).resolve().exists():
            bad.append(f'{f}: {u}')
print('\n'.join(bad) if bad else 'links and fences OK')
PY

# every bash block parses
python3 - <<'PY'
from pathlib import Path
import re, subprocess
FENCE = chr(96) * 3
bad = 0
for f in sorted(Path('.').rglob('*.md')):
    for m in re.finditer(FENCE + r'bash\n(.*?)' + FENCE, f.read_text(), re.S):
        code = m.group(1)
        if re.search(r'<[a-z_]+>', code):
            continue                     # reference block with placeholders
        if subprocess.run(['bash', '-n'], input=code, text=True, capture_output=True).returncode:
            print('bash error in', f)
            bad += 1
print('bash blocks OK' if not bad else f'{bad} broken')
PY
```

## Reporting without a pull request

Open an issue with your `claude --version`, your `sw_vers`, the chapter, the exact command, and
what happened instead. That is enough for someone else to reproduce it.

## Licensing of contributions

By contributing you agree your changes are released under the same license as the repository
([CC BY 4.0](LICENSE)). See [`NOTICE.md`](NOTICE.md) for attribution and trademark details.