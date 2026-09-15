# 03 — Configuração

**Português** · [English](../03-configuration.md)

Configuração é duas coisas diferentes:

1. **Configurações** (`settings.json`) — comportamento mecânico: permissões, modelo, tema.
2. **Instruções** (`CLAUDE.md`) — o que o Claude deve *saber* sobre o seu projeto.

A segunda importa muito mais para a qualidade do resultado. A maior parte deste capítulo é sobre isso.

**Tempo:** 25 minutos.

---

## Parte 1 — `CLAUDE.md`

Um arquivo Markdown de instruções permanentes, carregado automaticamente no início de cada sessão.

### Deixe o Claude escrever o primeiro rascunho

```text
/init
```

Isso analisa o seu projeto e gera um `CLAUDE.md`. **Depois edite-o** — o `/init` produz um
rascunho razoável, não um arquivo pronto.

### Onde ele fica

| Caminho | Aplica-se a |
|---|---|
| `~/.claude/CLAUDE.md` | Você, em todos os projetos |
| `<project>/CLAUDE.md` | Todos no projeto — **faça commit deste arquivo** |

### O que entra nele

O teste: *um colega novo precisaria disto no primeiro dia?*

**Inclua:** comandos exatos de build/test/lint/execução · stack e versões · estrutura de diretórios
para tudo que não seja óbvio · convenções (nomes, tratamento de erros, estilo de commit) ·
armadilhas ("a pasta `legacy/` está congelada") · o que não tocar.

**Exclua:** tudo que dá para deduzir lendo o código · tutoriais longos · segredos · regras
aspiracionais que ninguém segue.

### Um exemplo real

*O exemplo está em inglês porque esse é o idioma mais comum nesses arquivos, mas ele funciona igual escrito em português.*

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

### Mantenha-o curto

O `CLAUDE.md` custa contexto em **todas** as mensagens. Mire em 50–150 linhas. Se passar disso, divida
e referencie o resto.

### Duas coisas para saber

**Edições não valem para a sessão em execução.** Ele é lido uma vez, na inicialização. Reinicie, ou
use `/clear` depois de editar.

**Verifique se ele carregou** com `/context`.

---

## Parte 2 — Configurações

### Onde ficam as configurações

| Escopo | Arquivo | Afeta |
|---|---|---|
| **User** | `~/.claude/settings.json` | Você, em todos os projetos |
| **Shared project** | `<project>/.claude/settings.json` | Todos — **faça commit deste arquivo** |
| **Project local** | `<project>/.claude/settings.local.json` | Você, neste projeto — não faça commit |

Se a mesma chave aparecer em dois arquivos, a mais alta vence:
`project local > shared project > user`.

**Listas são mescladas em vez de sobrescritas.** Se o seu arquivo de usuário libera `Bash(npm test)`
e o arquivo do projeto libera `Bash(npm run lint)`, as duas valem.

### Um arquivo de usuário seguro para começar

Crie `~/.claude/settings.json`:

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": ["Read", "Bash(git status)", "Bash(git diff *)"],
    "deny": ["Read(./.env)", "Read(./.env.*)"]
  }
}
```

A linha `$schema` dá autocomplete no VS Code e em outros editores.

> Arquivos de configurações são JSON estrito: sem comentários, sem vírgula final. Uma vírgula final
> produz um `Settings Error` na próxima inicialização.

### Regras de permissão

As regras têm o formato `Tool(pattern)`, em três listas:

| Lista | Significado |
|---|---|
| `allow` | Executa sem perguntar |
| `ask` | Sempre pergunta, mesmo no modo Auto |
| `deny` | Nunca executa — **vence qualquer modo** |

Regras `ask` são como você mantém um checkpoint humano no modo Auto:

```json
{
  "permissions": {
    "ask": ["Bash(git push *)"]
  }
}
```

Essa é a clássica: deixe o Claude fazer tudo, mas confirme sempre antes de o código sair da sua
máquina.

> As regras comparam o comando **como escrito**. `git push` e `git -C /path push` são strings
> diferentes.

### Verifique e altere as configurações

```text
/status
```

A linha `Setting sources` lista os arquivos que realmente carregaram. Se o seu não estiver lá, ele não
carregou — normalmente um caminho errado ou JSON inválido.

```text
/config
```

Abre um menu de opções comuns e as grava para você.

Para erros de validação, rode `claude doctor` no seu shell.

---

## Parte 3 — Modelo

```text
/model
```

Salva a sua escolha como padrão para novas sessões.

O **nível de esforço** controla o quanto ele pensa — mais alto custa mais e vale a pena para depuração
difícil, não para erros de digitação:

```text
/effort
```

Para definir um padrão de projeto, em `<project>/.claude/settings.json`:

```json
{ "model": "claude-sonnet-5" }
```

> Trocar de modelo no meio da sessão invalida o cache de prompt, então o próximo turno relê toda a
> conversa sem cache. Escolha um e fique nele.

---

## Checklist

- [ ] `<project>/CLAUDE.md` existe, tem menos de ~150 linhas e traz comandos reais
- [ ] `/context` mostra que ele carregou
- [ ] `~/.claude/settings.json` existe com uma linha `$schema`
- [ ] regras `deny` protegem os seus arquivos `.env`
- [ ] uma regra `ask` protege o `git push`
- [ ] `/status` lista os arquivos de configurações que você espera
- [ ] `claude doctor` não reporta erros de validação

Próximo: **[04 — Fluxo de trabalho](04-workflow.md)**