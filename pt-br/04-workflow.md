# 04 — Fluxo de trabalho, ferramentas e extensões

**Português** · [English](../04-workflow.md)

Três partes: o loop que você roda todo dia, conectar ferramentas externas e estender o Claude Code
quando o básico já estiver confortável.

**Tempo:** 60 minutos para as três. Só a Parte 1 leva 20.

---

# Parte 1 — O loop diário

```text
1. Entender   →  perguntas somente leitura, ou plan mode
2. Isolar     →  uma branch git
3. Trabalhar  →  descreva a mudança
4. Verificar  →  rode os testes, leia o diff
5. Commit     →  uma mensagem que você mesmo escreveria
6. Limpar     →  /clear antes da próxima tarefa
```

Pular a etapa 1 desperdiça tokens. Pular a etapa 4 é como bugs chegam em produção.

## 1. Entenda primeiro

```text
como funciona a autenticação neste projeto?
```

```text
liste todos os lugares que leem o arquivo de config
```

Para qualquer coisa não trivial, use o plan mode para que a exploração termine com uma abordagem
acordada:

```text
/plan migre o carregador de configurações de JSON para TOML
```

## 2. Isolar

```bash
git checkout -b feature/rate-limiting
```

## 3. Escreva prompts precisos

| Fraco | Forte |
|---|---|
| "corrija o bug" | "corrija o bug em que o login mostra uma tela em branco depois de uma senha errada" |
| "adicione validação" | "adicione validação de e-mail no formulário de cadastro; rejeite endereços sem `@`; retorne um 422" |
| "refatore isto" | "extraia a lógica de retry de `client.py` para `retry.py` com a mesma API pública; mantenha os testes passando" |

Padrões que funcionam:

- **Etapas numeradas** para trabalho com várias partes: `1. adicione uma coluna 2. defina na gravação 3. exponha na API`
- **Aponte exemplos:** `siga o mesmo padrão de src/routes/invoices.py`
- **Restrinja o raio de impacto:** `mude apenas arquivos em src/api/`
- **Peça um plano primeiro:** `me diga seu plano e espere minha aprovação`

Quando dá errado: `Esc` para interromper, `/rewind` para desfazer, `/clear` e reformule se ele
estiver andando em círculos.

## 4. Verifique

Nunca aceite uma mudança que você não verificou.

```text
/diff
```

```text
rode os testes
```

```text
rode `make lint` e corrija tudo que ele reportar
```

O padrão de maior valor deste guia — **reproduza, depois corrija**:

```text
escreva um teste que reproduza o bug, depois corrija o bug
```

Um teste que falha primeiro prova que o bug existe e que a correção funciona.

Depois, peça que ele revise o próprio trabalho:

```text
revise suas próprias mudanças em busca de bugs e casos de borda
```

## 5. Commit

```text
faça commit das minhas mudanças com uma mensagem descritiva
```

Leia a mensagem e confirme:

```bash
git log -1 --stat
```

## 6. Gerencie o contexto

O contexto é seu recurso mais escasso. Quando ele enche, o Claude Code compacta — com perdas.

| Comando | Efeito |
|---|---|
| `/context` | Mostra o que ocupa a janela |
| `/compact` | Compacta agora, opcionalmente mantendo fatos nomeados |
| `/clear` | Apaga a conversa, mantém os arquivos |
| `/usage` | Uso do plano e o que o consome |

**Limpe entre tarefas não relacionadas.** Terminou o bug de autenticação? `/clear` antes do trabalho
na UI.

**Compacte de propósito**, com orientação:

```text
/compact mantenha as decisões sobre a estratégia de retry e a lista de arquivos alterados
```

**Prefira leituras específicas:** `leia src/routes/users.py` é melhor que `leia a pasta src/ inteira`.

## Sessões

| Comando | Efeito |
|---|---|
| `claude -c` | Continua a sessão mais recente aqui |
| `claude -r` | Escolhe uma sessão anterior |
| `/rename` | Nomeia a sessão atual |

## Scripts

```bash
claude -p "explain what src/parser.py does"
git diff main | claude -p "review this diff for bugs"
```

Para CI, adicione `--permission-mode dontAsk --allowedTools "Bash(npm test)" "Read"` para que ele
nunca espere por entrada.

---

# Parte 2 — Conecte ferramentas externas (MCP)

**MCP** permite que o Claude alcance coisas fora do seu projeto: Google Drive, um issue tracker, um
banco de dados, um navegador. Um **servidor** MCP fornece as ferramentas; o Claude Code é o cliente.

**Conecte um quando você se pegar copiando dados para o chat.**

Duas palavras que você precisa:

- **Escopo** — `local` (você, esta pasta), `project` (todos que clonarem), `user` (você, em todo lugar).
- **Transporte** — `http` para um serviço hospedado, `stdio` para um programa no seu Mac.

## Prática: um servidor sem conta

```bash
claude mcp add --transport http claude-code-docs https://code.claude.com/docs/mcp
claude mcp list
```

Você quer ver `✔ Connected`. Depois, em uma sessão:

```text
Use o servidor claude-code-docs para descobrir o que MCP_TIMEOUT faz
```

Remova quando terminar: `claude mcp remove claude-code-docs`.

## Google Drive — a forma recomendada

Sem projeto no Google Cloud, sem credenciais para gerenciar.

> **Exige** estar logado com uma conta claude.ai. Não funciona com uma API key. Verifique com
> `/status`.

1. Abra <https://claude.ai/customize/connectors> e adicione o **Google Drive**.
2. Conclua o login no Google por lá.
3. Inicie o Claude Code e verifique `/mcp` — o Google Drive aparece.
4. Teste a leitura primeiro: `liste meus 5 arquivos modificados mais recentemente no Google Drive`
5. Teste a escrita: `crie um Google Doc chamado "Test" com uma frase`

### Opcional: a forma auto-hospedada

Pule isto a menos que você queira especificamente seu próprio projeto no Google Cloud em vez do
conector gerenciado.

1. `brew install node`
2. No [Google Cloud Console](https://console.cloud.google.com/), ative as APIs do Drive, Docs,
   Sheets, Slides e Calendar, depois crie um cliente OAuth do tipo **Desktop app**.
3. Salve o JSON baixado como `~/.config/google-drive-mcp/gcp-oauth.keys.json`.
4. Autorize uma vez:

```bash
npx -y @piotr-agier/google-drive-mcp auth
```

5. Registre o servidor:

```bash
claude mcp add --scope user \
  --env GOOGLE_DRIVE_OAUTH_CREDENTIALS="$HOME/.config/google-drive-mcp/gcp-oauth.keys.json" \
  google-drive -- npx -y @piotr-agier/google-drive-mcp
```

O `--` separa as opções do próprio Claude Code do comando que ele executa. Tudo depois dele é
repassado ao servidor sem alteração — sem ele, o Claude Code tenta ler `-y` como se fosse uma flag
sua.

## Gerenciando servidores

```bash
claude mcp list                  # what is configured, and health
claude mcp get <name>            # details
claude mcp remove <name>         # remove
```

Dentro de uma sessão, `/mcp` mostra todos os servidores e permite autenticar ou desligar um deles.

## Segurança, brevemente

Servidores MCP **executam código no seu Mac**, e a saída deles entra no contexto do Claude — então
um servidor que busca conteúdo web pode carregar uma injeção de prompt. Conecte servidores em que
você confia. Revise a contagem de ferramentas em `/mcp`: um servidor com 100+ ferramentas é
conveniente e caro.

---

# Parte 3 — Estendendo o Claude Code

> **Opcional.** Volte quando perceber que está repetindo as mesmas instruções.

Quatro mecanismos. Prefira o mais simples que funcione.

| Você quer | Use |
|---|---|
| Um procedimento reutilizável | **Skill** |
| Conhecimento fixo do projeto | `CLAUDE.md` (capítulo 3) |
| Um especialista que trabalha no próprio contexto | **Subagente** |
| Algo que aconteça automaticamente em um evento | **Hook** |
| Compartilhar tudo isso como uma unidade | **Plugin** |

## Skills

Uma pasta com um `SKILL.md`. O Claude a encontra pela descrição; você também pode invocá-la com
`/name`.

`~/.claude/skills/release/SKILL.md`:

*Os arquivos de exemplo (a skill e o subagente) continuam em inglês, como no capítulo 3.*

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

O campo `description` é o que o Claude usa para encontrar a skill — ele precisa dizer *quando* usá-la.

## Subagentes

Um Claude separado, com a própria janela de contexto. Bom para buscas amplas e revisões repetidas
que, de outra forma, inundariam sua conversa.

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

Use-o com `use o subagente security-reviewer nas mudanças desta branch`.

> Subagentes não podem fazer perguntas no meio da tarefa. Dê a eles um briefing completo.

## Hooks

Comandos de shell executados em eventos do ciclo de vida. Determinísticos — rodam independentemente
de o modelo decidir rodá-los.

Este notifica você quando o Claude precisa de entrada. Em `~/.claude/settings.json`:

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

E este formata todos os arquivos que o Claude edita:

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

Esse comando longo lê o caminho do arquivo editado da entrada JSON do hook e roda o `prettier` nele.
Ele usa `python3`, que você já tem, em vez de `jq`, que você teria de instalar. Ele precisa do Node
da Parte 2 para o `npx`.

Depure hooks com `/hooks` — um hook que falha em silêncio é o problema mais comum.

## Plugins

Um pacote do que está acima, instalável como uma unidade.

```text
/plugin marketplace add anthropics/claude-plugins-official
/plugin install mcp-server-dev@claude-plugins-official
```

`/plugin` abre o gerenciador.

---

## Checklist

- [ ] Rodei uma tarefa pelo loop completo, incluindo `git diff` antes do commit
- [ ] Usei o plan mode pelo menos uma vez
- [ ] Conectei um servidor MCP e o usei
- [ ] Escrevi uma skill ou um hook

Próximo: **[05 — Referência](05-reference.md)**