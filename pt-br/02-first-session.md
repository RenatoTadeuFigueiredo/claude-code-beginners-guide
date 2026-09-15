# 02 — Sua primeira sessão

**Português** · [English](../02-first-session.md)

Este capítulo leva você por uma sessão real e ensina a única coisa que separa o uso agradável
do Claude Code do uso doloroso: **modos de permissão.**

**Tempo:** 20 minutos.

---

## 1. Início

```bash
cd ~/Projects/your-project
claude
```

> **Sempre inicie o Claude Code na pasta do projeto em que você quer trabalhar.** Essa pasta se torna
> o diretório de trabalho da sessão. Arquivos dentro dela são tratados como seus; arquivos fora são externos.

Ainda sem projeto? Crie um:

```bash
mkdir -p ~/Projects/hello-claude && cd ~/Projects/hello-claude && git init
claude
```

---

## 2. Pergunte antes de mudar qualquer coisa

Exploração somente leitura é o primeiro passo mais seguro:

```text
o que este projeto faz?
```

```text
explique a estrutura de pastas
```

Note que você não precisou nomear os arquivos. O Claude explora sozinho.

---

## 3. Modos de permissão

Este é o conceito central de segurança. Um modo de permissão decide o que o Claude pode fazer sem
perguntar.

Pressione **`Shift+Tab`** para alternar entre os modos. A barra de status mostra o modo ativo.
Existem **quatro que você precisa**, exatamente os quatro pelos quais `Shift+Tab` alterna:

| Modo | Executa sem perguntar | Use para |
|---|---|---|
| **Manual** | Apenas leituras | Trabalho sensível, código desconhecido, revisar tudo |
| **Accept edits** | Leituras, edições de arquivos, comandos comuns de sistema de arquivos | Iterar em código que você vai revisar depois |
| **Plan** | Leituras, mais comandos aprovados — **sem edições** | Explorar antes de mudar |
| **Auto** | Tudo, com um classificador de segurança na frente | Tarefas longas, menos prompts |

> **Opcional.** Existem mais dois modos (`dontAsk`, `bypassPermissions`). Eles precisam de uma flag
> de linha de comando, então você pode ignorá-los. O capítulo 5 os lista.

**Sua sessão começa em Auto** nos planos Pro, Max e Team, e em Manual no Enterprise ou com uma
chave de API do Console. Para forçar um modo:

```bash
claude --permission-mode plan
```

### O que o Auto realmente faz

Não é "sem verificações". Um modelo classificador separado revisa cada ação e bloqueia coisas que
parecem arriscadas. Por padrão ele bloqueia `curl | bash`, `git push --force`, `git reset --hard`,
`terraform destroy`, exclusão em massa na nuvem e commits que vazam segredos. Ele permite operações
locais em arquivos, instalar dependências declaradas e fazer push para um branch do seu próprio repo.

Se ele bloquear algo três vezes seguidas, o Auto pausa e volta a perguntar.

Veja as listas completas com:

```bash
claude auto-mode defaults
```

### Duas coisas que nenhum modo aprova automaticamente

**Caminhos protegidos** — `.git/`, `.claude/`, `.vscode/`, `.idea/`, e arquivos como `.zshrc`,
`.gitconfig`, `.mcp.json`.

**Caminhos críticos** — `rm` na sua pasta home, `/`, diretórios de nível superior como `/usr`, e seu
diretório de trabalho. Um disjuntor contra um glob ruim.

---

## 4. Faça uma mudança

Pressione `Shift+Tab` até ver `accept edits on`, depois:

```text
crie hello.py com uma função que imprime uma saudação
```

Leia o que ele escreveu (`cat hello.py`), depois:

```text
rode hello.py
```

---

## 5. Interromper e desfazer

| Ação | Como |
|---|---|
| **Interromper o que o Claude está fazendo** | `Esc` |
| Desfazer — arquivos *e* conversa | `/rewind` |
| Ver o que mudou | `/diff` |
| Descartar todas as mudanças não commitadas | `git checkout -- .` |
| Sair | `Ctrl+D` duas vezes |

**`Esc` é a tecla mais importante do Claude Code.** Se ele estiver fazendo algo inesperado,
pressione. Nada se perde — você redireciona e continua.

`/rewind` é o desfazer embutido e funciona até para mudanças que você já aprovou.

> **Se você se perder:** `Esc`, depois `/rewind`. Isso resolve quase tudo.

---

## 6. Três hábitos

### Trabalhe no Git

O Git torna tudo reversível e revisável.

```bash
git add -A && git commit -m "checkpoint before Claude works"
```

Faça commit antes de deixar o Claude fazer qualquer coisa substancial. Depois use `git diff` para
revisar e `git checkout -- .` para jogar tudo fora.

### Leia o diff antes de confiar nele

Aprovar uma edição não é ler. Use `/diff` durante a sessão e `git diff` depois. Se você não
consegue explicar a mudança, não faça commit.

### Combine o modo com o risco

| Situação | Modo |
|---|---|
| Primeira vez em um repo desconhecido | Manual |
| Revisando código desconhecido | Manual |
| Iterando em código que você conhece | Accept edits |
| "Não sei o que mudar ainda" | Plan |
| Refatoração longa ou loop de correção de testes | Auto |

---

## 7. Modo Plan

O recurso mais subutilizado, e o mais adequado para qualquer coisa não trivial.

```text
/plan adicione rate limiting à API
```

O Claude pesquisa e apresenta um plano **sem editar**. Depois você escolhe aprová-lo (e começar a
trabalhar) ou continuar planejando. `Ctrl+G` abre o plano no seu editor antes de você aprovar.

Use quando a tarefa abrange vários arquivos, ou quando você quer concordar com um design antes de
o código aparecer.

---

## 8. O que NÃO fazer

- **Não deixe ele trabalhar em mudanças não commitadas que você se importa.** Faça commit primeiro.
- **Não cole segredos na conversa.** O Claude pode escrevê-los em um arquivo ou em um commit.
- **Não confie cegamente na saída.** O Claude pode estar errado com confiança.
- **Não aprove edições que você não leu.**
- **Não deixe ele sem supervisão em um repo de produção** até ter visto como ele se comporta.

---

## Checklist

- [ ] Começou a partir da pasta do projeto
- [ ] Fez uma pergunta somente leitura primeiro
- [ ] Alternou os modos com `Shift+Tab`
- [ ] Fez uma pequena mudança e leu
- [ ] Testou `Esc`
- [ ] Testou `/rewind`
- [ ] Fez um commit no Git e o inspecionou com `git diff`

Próximo: **[03 — Configuração](03-configuration.md)**