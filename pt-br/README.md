# Claude Code — um guia para iniciantes

**Português** · [English](../README.md)

Um caminho prático do zero à produtividade com [Claude Code](https://code.claude.com/docs) no macOS
e no Windows: instalar, entender as permissões, configurar bem e usar em um projeto real.

Escrito para quem **já usa ferramentas de IA e já abriu um terminal**, mas não se chamaria de
avançado em TI. Nenhum conhecimento de programação é presumido.

**Não oficial.** Um guia independente pela documentação pública da Anthropic — veja
[`NOTICE.md`](../NOTICE.md).

---

## Para quem é este guia

**Sim, se você:** já usa ChatGPT/Claude/etc. · digita comandos com naturalidade · usa macOS ou
Windows · quer usar um agente de IA em código real sem quebrar nada.

**Não, se você:** nunca abriu um terminal (leia o
[guia de terminal](https://code.claude.com/docs/en/terminal-guide) primeiro) · procura uma
referência de API (use a [documentação oficial](https://code.claude.com/docs)) · quer um
levantamento de todas as opções (este guia escolhe um caminho por tarefa, de propósito).

---

## O que você recebe

Seis arquivos curtos. Os capítulos 1 a 4 são o caminho; o capítulo 5 é referência.

| # | Capítulo | Leia quando |
|---|---|---|
| 1 | [01-setup.md](01-setup.md) | Agora — pré-requisitos, instalação, login |
| 2 | [02-first-session.md](02-first-session.md) | Logo depois — permissões, segurança, desfazer |
| 3 | [03-configuration.md](03-configuration.md) | Depois da primeira sessão. `CLAUDE.md` é o mais importante |
| 4 | [04-workflow.md](04-workflow.md) | Quando estiver confortável — rotina diária, Git, ferramentas externas |
| 5 | [05-reference.md](05-reference.md) | Para consultar. **Leia a primeira seção antes de precisar** |

O capítulo 5 começa com recuperação, porque a forma mais rápida de deixar de temer um agente que
edita seus arquivos é saber como desfazer.

---

## A versão de 5 minutos

**macOS**

```bash
curl -fsSL https://claude.ai/install.sh | bash    # install
```

**Windows**

```powershell
irm https://claude.ai/install.ps1 | iex           # install
```

```bash
claude --version                                  # confirm
```

```bash
cd ~/Projects/my-project                          # start
claude
```

Faça login pelo navegador quando for pedido. Depois:

```text
o que este projeto faz?
```

Esse é o ciclo inteiro. O guia é sobre fazer isso *bem* e *com segurança*.

**Requisitos:** macOS 13+, ou Windows 10 (1809+) / Server 2019+ em x64 ou ARM64 — 32 bits não é
suportado · uma conta Claude **Pro, Max, Team ou Enterprise** (o plano gratuito não inclui o
Claude Code) · um terminal.

---

## Três ideias que explicam o resto

**1. Ele roda em loop.** O Claude Code não responde uma vez e para. Ele lê, age, observa e
repete até a tarefa terminar. Um pedido vago se perde; um preciso acerta.

**2. Ferramentas têm permissões.** Ele pode ler arquivos, escrever arquivos, rodar comandos de
shell, buscar páginas web. Toda chamada está sujeita a um modo de permissão que você controla.
O capítulo 2 cobre isso direito — é o capítulo mais importante de todos.

**3. Contexto é o recurso escasso.** A conversa tem um orçamento fixo. Quando ele enche, o
Claude Code compacta — com perdas. O capítulo 4 ensina hábitos que mantêm as sessões enxutas.

---

## Quão atual isto é?

| | |
|---|---|
| **Escrito para** | Claude Code `2.1.x` |
| **Última revisão** | 2026-09-15 |
| **Cadência** | Claude Code lança toda semana |

O Claude Code muda rápido. Trate a data de revisão como validade do *detalhe*, não da abordagem:
modos de permissão e a rotina diária mudam devagar, flags de comando e chaves de configuração
mudam rápido.

**Onde o guia e a documentação oficial discordarem, a documentação vence.** Se você encontrar
divergência, uma issue com seu `claude --version` basta para corrigir — veja
[`CONTRIBUTING.md`](../CONTRIBUTING.md).

O que muda mais rápido, se você quiser conferir: `claude --version` contra a versão do guia ·
`/help` contra o capítulo 5 · nomes de modo de permissão contra
<https://code.claude.com/docs/en/permission-modes>.

---

## Convenções

- Blocos `bash` são digitados no seu terminal. Blocos `text` são digitados dentro do Claude Code.
  Blocos `json` são conteúdos de arquivo que você cria.
- Blocos rotulados **macOS** ou **Windows** valem só para aquele sistema. Blocos sem rótulo
  funcionam nos dois — um bloco `powershell` só aparece onde o comando diverge.
- `~` significa sua pasta home (`/Users/yourname`).
- **Um caminho recomendado por tarefa.** Onde existem várias abordagens, o guia escolhe uma para
  você não ter que escolher entre coisas que ainda não sabe comparar.
- Blocos de citação marcados como **Opcional** podem ser pulados na primeira leitura.

---

## Documentação oficial

Os links abaixo estão em inglês.

| Tópico | URL |
|---|---|
| Início da documentação | <https://code.claude.com/docs> |
| Início rápido | <https://code.claude.com/docs/en/quickstart> |
| Boas práticas | <https://code.claude.com/docs/en/best-practices> |
| Fluxos comuns | <https://code.claude.com/docs/en/common-workflows> |
| Referência de configurações | <https://code.claude.com/docs/en/settings-reference> |
| Permissões | <https://code.claude.com/docs/en/permissions> |
| Modos de permissão | <https://code.claude.com/docs/en/permission-modes> |
| MCP | <https://code.claude.com/docs/en/mcp> |
| Skills | <https://code.claude.com/docs/en/skills> |
| Subagentes | <https://code.claude.com/docs/en/sub-agents> |
| Hooks | <https://code.claude.com/docs/en/hooks> |
| Plugins | <https://code.claude.com/docs/en/plugins> |
| Referência de erros | <https://code.claude.com/docs/en/errors> |
| Changelog | <https://code.claude.com/docs/en/changelog> |

---

## Licença

Texto licenciado sob [CC BY 4.0](../LICENSE) — compartilhe e adapte livremente, inclusive
comercialmente, com atribuição.

Não oficial e fornecido como está. **Claude** e **Claude Code** são marcas da Anthropic PBC.
Detalhes completos de atribuição e marcas em [`NOTICE.md`](../NOTICE.md).

---

Próximo: **[01 — Setup](01-setup.md)**