# 01 — Setup

**Português** · [English](../01-setup.md)

Pré-requisitos, instalação e login. Ao final, você tem o Claude Code rodando.

**Tempo:** 25 minutos, a maior parte esperando downloads.

Se `~`, JSON e `git` já são familiares, pule para a [seção 4](#4-instalar).

---

## 1. O que você precisa

| Requisito | Por quê |
|---|---|
| macOS 13 (Ventura) ou superior | O Claude Code exige isso |
| Uma conta Claude **Pro, Max, Team ou Enterprise** | O plano gratuito não inclui o Claude Code |
| Um terminal | Você já usou um |

Você não precisa programar.

---

## 2. Seis comandos de terminal, e o que é JSON

Você só precisa destes seis comandos.

| Comando | Faz |
|---|---|
| `cd <folder>` | Entra em uma pasta |
| `cd ..` | Sobe uma pasta |
| `ls` | Lista o que está aqui |
| `mkdir -p <path>` | Cria uma pasta |
| `cat <file>` | Imprime um arquivo |
| `pwd` | Imprime onde você está |

`Tab` completa o que você está digitando. `↑` traz de volta seu último comando. `pwd` diz onde você
está se você se perder.

### `~` significa sua pasta home

`~` **é** `/Users/yourname`. Então `~/.claude/settings.json` é
`/Users/yourname/.claude/settings.json`.

### JSON é um formato de texto, com três regras

Você vai editar JSON no capítulo 3.

**Chaves delimitam um objeto.** Chaves e valores separados por dois-pontos, itens por vírgulas:

```json
{ "name": "my-project", "version": 2 }
```

**Colchetes delimitam uma lista:**

```json
{ "tags": ["api", "internal"] }
```

**Strings precisam de aspas duplas.** Números e `true`/`false` não.

Dois erros quebram JSON sempre: uma **vírgula final**, e **comentários** (JSON não tem `//`).

```json
{
  "port": 8000,
  "enabled": true,
}
```

Esse exemplo está deliberadamente quebrado — a vírgula depois de `true` precisa sair.

Verifique qualquer arquivo JSON com:

```bash
python3 -m json.tool ~/.claude/settings.json
```

Silêncio significa válido.

---

## 3. Instalar três ferramentas

### `git` — como você acompanha e desfaz mudanças

```bash
xcode-select --install
```

Um diálogo aparece. Clique em **Install** e espere. Depois:

```bash
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

> Se você rodou um comando `git` antes e viu um popup, era isso. Deixe terminar.

### Homebrew — para instalar uma ferramenta depois

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Ele pede a senha do seu Mac — você não verá caracteres enquanto digita. Quando ele
termina, imprime duas ou três linhas terminando em `>> ~/.zprofile`. **Rode essas linhas**
(elas colocam o Homebrew no seu `PATH`), depois confira:

```bash
brew --version
```

> **`PATH`** é a lista de pastas que seu shell procura por comandos. Uma ferramenta que não está
> no `PATH` dá `command not found` mesmo estando instalada.

### Uma pasta de projetos

```bash
mkdir -p ~/Projects
```

Mantenha todo projeto dentro de `~/Projects` para os comandos seguintes ficarem previsíveis.

---

## 4. Instalar

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Essa é a instalação inteira. Ele baixa o binário, guarda arquivos versionados em
`~/.local/share/claude/versions/`, e cria um launcher em `~/.local/bin/claude`.

**Ele se mantém atualizado.** Você nunca roda um comando de upgrade.

> Este guia usa apenas o instalador nativo. Instruções em outros lugares podem mencionar `npm` ou
> Homebrew — esses também funcionam, mas você não precisa deles.

---

## 5. Verificar

```bash
claude --version
```

Esperado: algo como `2.1.271 (Claude Code)`.

### Se você receber `command not found`

Um problema de `PATH`, não uma instalação quebrada. Confirme que o launcher existe:

```bash
ls -l ~/.local/bin/claude
```

Se ele existir, adicione a pasta ao seu `PATH`:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Tente de novo. Abra uma nova janela de terminal se ainda falhar.

Se o `ls` disser que o arquivo está faltando, a instalação não terminou — rode de novo e leia a saída.

### Diagnóstico completo

```bash
claude doctor
```

Imprime a saúde da instalação, erros de configurações e correções sugeridas sem iniciar uma sessão.

---

## 6. Fazer login

```bash
cd ~/Projects
claude
```

Seu navegador abre. Faça login, depois volte ao terminal.

> Se o navegador não conseguir alcançar `localhost`, o Claude Code imprime uma URL em vez disso. Faça
> login, copie o endereço **completo** da barra de endereços do seu navegador, e cole no terminal.

Confirme a conta:

```text
/status
```

As credenciais vão para o Keychain do macOS e se renovam automaticamente. Para trocar de conta
depois, rode `/login` dentro de uma sessão.

---

## 7. Onde as coisas ficam

| Path | Contém |
|---|---|
| `~/.local/bin/claude` | O launcher |
| `~/.claude/settings.json` | Suas configurações — você cria isto no capítulo 3 |
| `~/.claude/CLAUDE.md` | Instruções para todo projeto |
| `~/.claude.json` | O próprio arquivo de estado do Claude Code |
| `<project>/CLAUDE.md` | Instruções para um projeto |
| `<project>/.claude/settings.json` | Configurações para um projeto |

---

## 8. Atualizações e desinstalação

**Atualizações:** automáticas, em segundo plano, aplicadas no próximo início. Force uma com `claude update`.

Para menos surpresas, use o canal `stable` — cerca de uma semana atrás, pula regressões. Em
`~/.claude/settings.json`:

```json
{ "autoUpdatesChannel": "stable" }
```

**Desinstalar** — remova o programa:

```bash
rm -f ~/.local/bin/claude && rm -rf ~/.local/share/claude
```

Adicione `rm -rf ~/.claude && rm -f ~/.claude.json` para também apagar configurações e histórico.

---

## Checklist

- [ ] `git --version` funciona
- [ ] `brew --version` funciona
- [ ] `~/Projects` existe
- [ ] `claude --version` imprime uma versão
- [ ] `claude doctor` não reporta problemas bloqueantes
- [ ] Logado, e `/status` mostra a conta certa

Próximo: **[02 — Sua primeira sessão](02-first-session.md)**