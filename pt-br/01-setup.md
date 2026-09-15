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
| Windows 10 (1809) ou superior, ou Windows Server 2019 ou superior | O Claude Code exige isso |
| Uma máquina 64 bits (x64 ou ARM64) | 32 bits não serve |
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

Os seis também funcionam no PowerShell — `ls`, `cat`, `mkdir`, `cd` e `pwd` são aliases lá. Duas
ressalvas: `mkdir -p` dá erro se a pasta já existe, enquanto no Unix ele fica quieto, e o
encadeamento lá é com `;` onde o Unix usa `&&`.

`Tab` completa o que você está digitando. `↑` traz de volta seu último comando. `pwd` diz onde você
está se você se perder.

### `~` significa sua pasta home

`~` **é** sua pasta home: `/Users/yourname` no macOS, `%USERPROFILE%` no Windows (por exemplo
`C:\Users\yourname`). Então `~/.claude/settings.json` é `/Users/yourname/.claude/settings.json` no
macOS e `%USERPROFILE%\.claude\settings.json` no Windows.

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

**macOS**

```bash
xcode-select --install
```

Um diálogo aparece. Clique em **Install** e espere.

**Windows**

```powershell
winget install Git.Git
```

Opcional no Windows: instale só se você quiser a ferramenta Bash do Claude Code. Sem ele, o Claude
Code roda comandos pelo PowerShell.

Depois, nos dois sistemas:

```bash
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

> No macOS, se você rodou um comando `git` antes e viu um popup, era isso. Deixe terminar.

### Um gerenciador de pacotes — para instalar uma ferramenta depois

O macOS usa o **Homebrew**. O Windows já vem com o **winget**. Você precisa de um deles para uma
ferramenta que um capítulo posterior instala.

**macOS**

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Ele pede a senha do seu Mac — você não verá caracteres enquanto digita. Quando ele termina, imprime
duas ou três linhas terminando em `>> ~/.zprofile`. **Rode essas linhas** (elas colocam o Homebrew
no seu `PATH`).

**Windows**

```powershell
winget --version
```

Nada a instalar: o `winget` já vem com o Windows. Se isso não imprimir nada, instale o **App
Installer** pela Microsoft Store.

Depois, no macOS, confira que o Homebrew responde:

```bash
brew --version
```

> **`PATH`** é a lista de pastas que seu shell procura por comandos. As entradas são separadas por
> `:` no macOS e por `;` no Windows. Uma ferramenta que não está no `PATH` dá `command not found`
> (`not recognized` no Windows) mesmo estando instalada.

### Uma pasta de projetos

```bash
mkdir -p ~/Projects
```

Mantenha todo projeto dentro de `~/Projects` para os comandos seguintes ficarem previsíveis. No
Windows a mesma pasta é `%USERPROFILE%\Projects`.

---

## 4. Instalar

**macOS**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows**

```powershell
irm https://claude.ai/install.ps1 | iex
```

Essa é a instalação inteira. Ele baixa o binário, guarda arquivos versionados em
`~/.local/share/claude/versions/` (`%USERPROFILE%\.local\share\claude\versions\` no Windows), e cria
um launcher em `~/.local/bin/claude` (`%USERPROFILE%\.local\bin\claude.exe` no Windows).

**Ele se mantém atualizado.** Você nunca roda um comando de upgrade.

> No Windows, rode a linha do PowerShell acima. No CMD, use
> `curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd`.

> Este guia usa apenas o instalador nativo. Instruções em outros lugares podem mencionar `npm`,
> Homebrew ou `winget` — esses também funcionam, mas você não precisa deles.

---

## 5. Verificar

```bash
claude --version
```

Esperado: algo como `2.1.271 (Claude Code)`.

### Se você receber `command not found`

No Windows a mensagem é `'claude' is not recognized as the name of a cmdlet...`. De qualquer forma é
um problema de `PATH`, não uma instalação quebrada. Confirme que o launcher existe:

**macOS**

```bash
ls -l ~/.local/bin/claude
```

**Windows**

```powershell
Test-Path "$env:USERPROFILE\.local\bin\claude.exe"
```

Se ele existir, adicione a pasta ao seu `PATH`:

**macOS**

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

**Windows**

```powershell
[Environment]::SetEnvironmentVariable('PATH', "$([Environment]::GetEnvironmentVariable('PATH','User'));$env:USERPROFILE\.local\bin", 'User')
```

No Windows não existe arquivo rc: isso grava uma variável de usuário no registro, então reabra o
terminal para valer.

Tente de novo. Abra uma nova janela de terminal se ainda falhar.

Se a checagem disser que o arquivo está faltando, a instalação não terminou — rode de novo e leia a
saída.

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

As credenciais vão para o Keychain do macOS e, no Windows, para
`%USERPROFILE%\.claude\.credentials.json`, protegido pelas permissões do seu perfil de usuário. As
duas se renovam automaticamente. Para trocar de conta depois, rode `/login` dentro de uma sessão.

---

## 7. Onde as coisas ficam

| macOS | Windows | Contém |
|---|---|---|
| `~/.local/bin/claude` | `%USERPROFILE%\.local\bin\claude.exe` | O launcher |
| `~/.claude/settings.json` | `%USERPROFILE%\.claude\settings.json` | Suas configurações — você cria isto no capítulo 3 |
| `~/.claude/CLAUDE.md` | `%USERPROFILE%\.claude\CLAUDE.md` | Instruções para todo projeto |
| `~/.claude.json` | `%USERPROFILE%\.claude.json` | O próprio arquivo de estado do Claude Code |
| `<project>/CLAUDE.md` | `<project>/CLAUDE.md` | Instruções para um projeto |
| `<project>/.claude/settings.json` | `<project>/.claude/settings.json` | Configurações para um projeto |

---

## 8. Atualizações e desinstalação

**Atualizações:** automáticas, em segundo plano, aplicadas no próximo início. Force uma com `claude update`.

Para menos surpresas, use o canal `stable` — cerca de uma semana atrás, pula regressões. Em
`~/.claude/settings.json` (`%USERPROFILE%\.claude\settings.json` no Windows):

```json
{ "autoUpdatesChannel": "stable" }
```

**Desinstalar** — remova o programa:

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

Adicione isto para também apagar configurações e histórico:

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

- [ ] `git --version` funciona
- [ ] Seu gerenciador de pacotes responde: `brew --version` (macOS) ou `winget --version` (Windows)
- [ ] `~/Projects` existe (`%USERPROFILE%\Projects` no Windows)
- [ ] `claude --version` imprime uma versão
- [ ] `claude doctor` não reporta problemas bloqueantes
- [ ] Logado, e `/status` mostra a conta certa

Próximo: **[02 — Sua primeira sessão](02-first-session.md)**