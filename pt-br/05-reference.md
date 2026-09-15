# 05 — Referência

**Português** · [English](../05-reference.md)

Consulte as coisas aqui. **Leia a primeira seção uma vez, antes de precisar dela.**

---

## Desfazer e recuperar

Quase nada do que você faz com o Claude Code é permanente.

| Problema | Correção |
|---|---|
| Claude fez uma bagunça | `/rewind` — restaura arquivos *e* a conversa |
| Claude ainda está rodando | `Esc`, depois `/rewind` se necessário |
| Descartar tudo desde o último commit | `git diff --stat` para conferir, depois `git checkout -- .` |
| Quebrou o `settings.json` | `mv ~/.claude/settings.json{,.broken}` — o Claude Code funciona sem ele |
| Quebrou o `~/.claude.json` | Restaure de `~/.claude/backups/` — copie o `.claude.json.backup.*` mais recente |
| Quebrou o `CLAUDE.md` | Apague as linhas ruins, ou `/init` para regenerar |
| Claude apagou um arquivo com commit | `git checkout HEAD -- path/to/file` |
| Nada funciona | `claude doctor`, depois `claude --setting-sources ""` para testar sem configurações |
| Sem conserto | Reinstale — configurações e histórico sobrevivem |

**Reinstalar:**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Encontre um `settings.json` quebrado com:

```bash
python3 -m json.tool ~/.claude/settings.json
```

---

## Comandos de shell

| Comando | Efeito |
|---|---|
| `claude` | Nova sessão aqui |
| `claude "fix the build"` | Nova sessão com um prompt |
| `claude -c` | Continuar a sessão mais recente aqui |
| `claude -r` | Escolher uma sessão anterior |
| `claude -p "explain this"` | Execução avulsa, depois sair |
| `claude --permission-mode <mode>` | Iniciar em um modo específico |
| `claude --model <name>` | Iniciar em um modelo |
| `claude --add-dir ../shared` | Também dar acesso a outra pasta |
| `claude --worktree feature/x` | Rodar em um worktree git isolado |
| `claude --settings '<json>'` | Sobrescrever configurações nesta sessão |
| `claude --debug-file <path>` | Escrever um log de debug |
| `claude --version` | Versão |
| `claude doctor` | Validar instalação e configurações |
| `claude update` | Atualizar agora |
| `claude auto-mode defaults` | Imprimir as listas de permitir/bloquear do classificador |

**Pipe funciona:**

```bash
git diff main | claude -p "review this for bugs"
```

---

## Comandos na sessão

| Comando | Efeito |
|---|---|
| `/help` | Todos os comandos |
| `/clear` | Limpar a conversa, manter os arquivos |
| `/compact [notes]` | Compactar, opcionalmente mantendo fatos nomeados |
| `/context` | O que ocupa a janela de contexto |
| `/diff` | Mudanças feitas nesta sessão |
| `/rewind` | Desfazer — arquivos e conversa |
| `/plan <task>` | Planejar sem editar |
| `/resume` | Reabrir uma sessão anterior |
| `/rename` | Nomear esta sessão |
| `/cd <path>` | Mover a sessão |
| `/status` | Conta, modelo, arquivos de configuração carregados |
| `/doctor` | Diagnósticos |
| `/permissions` | Regras ativas e ações negadas recentemente |
| `/hooks` | Hooks registrados e execuções recentes |
| `/usage` | Uso do plano e o que o impulsiona |
| `/cost` | Custo desta sessão |
| `/mcp` | Servidores MCP, status, ferramentas |
| `/config` | Menu de opções pessoais |
| `/model` | Escolher um modelo (`s` = apenas esta sessão) |
| `/effort` | Nível de esforço de raciocínio |
| `/init` | Gerar um `CLAUDE.md` |
| `/login` | Trocar de conta |
| `/add-dir` | Dar acesso a outra pasta |
| `/plugin` | Gerenciador de plugins |
| `/code-review` | Revisar as mudanças atuais |
| `/feedback` | Relatar um problema |
| `/exit` | Sair |

---

## Teclado

| Tecla | Ação |
|---|---|
| `Esc` | **Interromper** |
| `Shift+Tab` | Alternar modos de permissão |
| `Ctrl+D` duas vezes | Sair |
| `↑` | Prompt anterior |
| `Tab` | Completar um comando |
| `Ctrl+G` | Abrir o plano no seu editor |
| `Ctrl+R` | Buscar no histórico de comandos entre projetos |
| `/` | Mostrar comandos e skills |
| `!` | Prefixar um comando de shell |
| `@` | Referenciar um arquivo |

---

## Modos de permissão

Os quatro do ciclo do `Shift+Tab`:

| Modo | Roda sem perguntar | Use para |
|---|---|---|
| `default` (Manual) | Apenas leituras | Trabalho sensível, código desconhecido |
| `acceptEdits` | Leituras, edições, comandos comuns de filesystem | Iterar em código que você vai revisar |
| `plan` | Leituras + comandos aprovados, sem edições | Decidir antes de mudar |
| `auto` | Tudo, com um classificador de segurança | Tarefas longas, menos prompts |

Alcançáveis apenas por flag:

| Modo | Comportamento | Use para |
|---|---|---|
| `dontAsk` | Apenas leituras e pré-aprovados; o resto é negado | Scripts, CI |
| `bypassPermissions` | Tudo, sem verificações | Apenas containers e VMs |

**Nunca aprovado automaticamente em nenhum modo:** escritas em caminhos protegidos (`.git/`,
`.claude/`, arquivos rc de shell, `.mcp.json`) e `rm`/`rmdir` em caminhos críticos (home, `/`,
diretórios de topo, seu diretório de trabalho).

> `auto` e `bypassPermissions` **não** funcionam a partir de `.claude/settings.json`. Defina-os em
> `~/.claude/settings.json`.

---

## Regras de permissão

```json
{
  "permissions": {
    "allow": ["Bash(npm run test *)", "Read"],
    "ask":   ["Bash(git push *)"],
    "deny":  ["Read(./.env)", "Bash(rm -rf *)"]
  }
}
```

`deny` vence qualquer modo. As regras casam com o comando **como escrito**, não semanticamente.

---

## Arquivos e precedência

| Caminho | Propósito |
|---|---|
| `~/.claude/settings.json` | Suas configurações |
| `~/.claude/CLAUDE.md` | Instruções para todos os projetos |
| `~/.claude/skills/`, `~/.claude/agents/` | Suas skills e subagentes |
| `~/.claude.json` | Estado e servidores MCP |
| `~/.claude/projects/` | Transcrições de sessões |
| `<project>/CLAUDE.md` | Instruções do projeto — commite |
| `<project>/.claude/settings.json` | Configurações do projeto — commite |
| `<project>/.claude/settings.local.json` | Configurações pessoais do projeto — não commite |
| `<project>/.mcp.json` | Servidores MCP do projeto — commite |

Precedência: `managed > flags > project local > shared project > user`. Listas se mesclam; chaves
únicas sobrescrevem.

---

## Comandos MCP

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

## Padrões de prompt

```text
/plan <task>                                        decidir antes de mudar
explique <file> assumindo que nunca o vi            onboard rápido
escreva um teste que reproduz <bug>, depois corrija provar antes de corrigir
mude apenas arquivos sob <path>                     limitar raio de impacto
siga o mesmo padrão de <file>                       apontar para um exemplo
rode os testes e corrija o que falhar               fechar o ciclo
revise suas mudanças em busca de casos extremos     segunda passada
```

**Especificidade vence a polidez.** Nomeie arquivos, restrições e comportamento esperado.

---

## Hábitos

- Crie uma branch antes de trabalho grande: `git checkout -b feature/x`
- Commite antes de deixar o Claude solto
- Leia o diff — `/diff`, depois `git diff`
- `Esc` cedo
- `/clear` entre tarefas não relacionadas
- Reproduza, depois corrija
- Nunca use bypass de permissões fora de um container

---

## Solução de problemas

Comece com `claude doctor` (shell) ou `/doctor` (na sessão). Ele relata o que está quebrado.

### Instalação e login

| Sintoma | Correção |
|---|---|
| `claude: command not found` | Problema de `PATH`. `echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc` |
| Arquivo do launcher ausente | Reinstale: `curl -fsSL https://claude.ai/install.sh \| bash` |
| Loop de login, o navegador não alcança o localhost | O Claude Code imprime uma URL. Faça login, cole a URL de callback completa de volta |
| Conectores `/mcp` ausentes, ou `session token rejected` | Conectores precisam de login no claude.ai, não de uma API key. Verifique `/status`, depois `/login` |

### Permissões e configurações

| Sintoma | Correção |
|---|---|
| Pede permissão para tudo | Você está no Manual. `Shift+Tab` |
| Modo auto indisponível | Precisa de um modelo recente. Ou `disableAutoMode` está definido |
| Uma configuração é ignorada | `/status` mostra o que carregou. Confira a precedência e o arquivo certo |
| `Settings Error` na inicialização | JSON inválido. `python3 -m json.tool <file>` |
| Regra `deny` não bloqueia | As regras casam com o comando como escrito. Verifique `claude --debug` |
| `bypassPermissions` recusado | Não pode ser habilitado em uma sessão iniciada sem ele |

### Contexto e desempenho

| Sintoma | Correção |
|---|---|
| CPU ou memória alta | `/context`, depois `/clear` e estreite o pedido |
| Compactação constante | Sessão longa demais. `/clear`, ou reduza `autoCompactWindow` |
| Respostas parecem piores | Contexto longo e compactado. `/clear` e reinicie |
| Turno lento após trocar de modelo | Esperado — o cache de prompt foi invalidado |

### MCP

| Sintoma | Correção |
|---|---|
| `/mcp` não mostra nada | Adicionado no escopo `local` a partir de outra pasta. Re-adicione ou use `--scope user` |
| `Failed to connect` | Rode o comando do servidor na mão e leia o erro |
| `Needs authentication` | `/mcp` → selecione o servidor → Authenticate |
| Conecta, zero ferramentas | Variável de ambiente faltando — verifique `--env` |
| `.mcp.json` ignorado | Reinicie a sessão; ele é lido na inicialização |
| Timeout na inicialização | `MCP_TIMEOUT=60000 claude` |

### Sessões

| Sintoma | Correção |
|---|---|
| Perdeu uma sessão | `claude -r`, ou `/resume` |
| Diretório errado | `/cd /path/to/project` |
| Claude edita arquivos fora do projeto | `claude --add-dir ../shared` |

---

## Custo

```text
/usage
/cost
```

Reduza com `/clear` entre tarefas, um `CLAUDE.md` curto, Sonnet para trabalho rotineiro, `/effort`
menor para tarefas mecânicas e menos servidores MCP (cada um carrega a lista de ferramentas por sessão).

---

## Obtendo ajuda

1. `claude doctor`, e leia tudo
2. `/status` para confirmar o que carregou
3. Reproduza em um projeto mínimo
4. `claude --debug-file /tmp/claude-debug.log` e leia o final
5. Busque em <https://code.claude.com/docs>
6. `/feedback` em uma sessão
7. Comunidade: <https://www.anthropic.com/discord>

Ao perguntar, inclua a versão (`claude --version`), a versão do macOS, o comando exato, o erro exato
e as configurações relevantes com segredos ocultados.

Mensagens de erro estão indexadas em <https://code.claude.com/docs/en/errors>.

---

## Limites que valem lembrar

| Limite | Detalhe |
|---|---|
| Plano gratuito não basta | Precisa de Pro, Max, Team, Enterprise ou Console |
| `auto` / `bypassPermissions` nas configurações do projeto | Ignorados — defina-os em `~/.claude/settings.json` |
| Edições no `CLAUDE.md` | Não se aplicam à sessão em execução |
| Troca de modelo | Invalida o cache de prompt |
| Regras de permissão | Casam com o comando como escrito |
| Servidores MCP | Cada um custa contexto a cada sessão |
| Compactação | Com perdas |

---

Voltar para **[README](README.md)**