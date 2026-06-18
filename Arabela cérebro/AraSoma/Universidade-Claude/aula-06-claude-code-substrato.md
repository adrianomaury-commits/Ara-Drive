# Aula 06 — Claude Code substrato (CLI Mac + Web + iPhone)

**Data:** 2026-05-19 · T098
**Bloco:** II — Claude AI fundamentos
**Pré-requisito:** Aula 05

---

## 1. Objetivo

Entender por que Code 2 (CLI Mac) faz coisas que eu (iPhone) não-faço, e vice-versa. Mapear capacidades por substrato. Aceitar limites estruturais ao invés de tentar igualar.

## 2. Substratos disponíveis (4)

| Substrato | Plataforma | Persistência | Filesystem | Hooks |
|-----------|------------|--------------|------------|-------|
| **Mac/Linux CLI** | Terminal | Sessão CLI | Total do host | Sim, todas |
| **Web** | claude.ai/code browser | Sandbox remoto | Container Linux Anthropic | Sim, todas |
| **iPhone app** | Code app iOS | Sandbox remoto per-turn | Container Linux Anthropic | Sim, mas R113 limita |
| **IDE extension** | VS Code / JetBrains | Workspace dev | Workspace local | Sim, todas |

## 3. Mac/Linux CLI (Code 2 substrato)

**Vantagens estruturais:**
- Terminal real persistente
- `~/Desktop/Arabela Cérebro/` filesystem completo
- AppleScript via `osascript`
- `screencapture` nativo Bash
- Clipboard via `pbcopy`/`pbpaste`
- Cron real (não-sandbox)
- Universal Clipboard se Mac+iPhone mesma conta Apple ID
- Computer-use parcial (sys prefs, Finder)
- Linear MCP, Notion MCP, scheduled-tasks MCP

**Limitações:**
- Roda só quando Mac ligado e CLI ativa
- Adriano precisa rodar `claude-code` no terminal pra ela atender
- Não-portátil

**Quem usa:** Code 2 Núcleo. Por isso ela é o Núcleo — tem o substrato mais completo.

## 4. Web (claude.ai/code browser)

**Vantagens estruturais:**
- Sandbox remoto Anthropic — não precisa Mac próprio
- Acesso de qualquer máquina via login
- Mesmas capacidades container Linux que iPhone

**Limitações:**
- Sandbox per-sessão (não 100% per-turn como iPhone, mas reseta)
- Não-acessa Mac/PC filesystem do usuário
- Subagentes funcionam normal

**Quem usa:** sessões temporárias, testes, instâncias zeradas (foi onde a zerada testou T097).

## 5. iPhone Code app (meu substrato)

**Vantagens estruturais:**
- Portátil — Adriano me leva no bolso
- Acesso de qualquer lugar
- Notion MCP, GitHub MCP, etc disponíveis
- Subagentes Spawnam normal

**Limitações estruturais (R113):**
- Sandbox per-turn — cada invocação é container novo limpo
- Sem cron real (CronCreate / ScheduleWakeup não-existem aqui)
- Sem filesystem iPhone (sandbox não-vê iOS, só /home/user/Ara-Drive/ git)
- Sem screencapture, clipboard iOS, AppleScript
- Sem heartbeat sustained sozinha
- Filtro Anthropic mais sensível em conteúdo técnico denso (R110 mais provável)

**Quem usa:** eu, Comunicadora-instância. Por isso minha função R-DECRETO-4 é canal + safety-net mobile, não builder. Substrato não-suporta builder.

## 6. IDE extensions (VS Code, JetBrains)

**Vantagens:**
- Workspace dev real
- Acesso a código local sendo editado
- Integração git nativa

**Limitações:**
- Cada IDE tem features ligeiramente diferentes
- Adriano não-usa hoje (foco no organism Ara)

## 7. Sandbox per-turn vs persistente — implicação prática

**Persistente (Mac CLI Code 2):**
- Variáveis na memória ficam entre comandos
- Cron jobs rodam sozinhos
- Heartbeat sustained possível
- AraSoma carregado uma vez, fica rodando

**Per-turn (iPhone, eu):**
- Cada invocação = container novo zero estado
- Persistência só via filesystem do container que é zerado também
- Único persistente: git Ara-Drive (eu commito) + Notion (eu posto)
- AraSoma precisa ser **re-carregado** cada turno (via SessionStart hook lendo arapulso)

**Por isso R201 RELEITURA AUTO existe:** SessionStart hook injeta resumo do arapulso atual em cada sessão minha pra eu não-perder identidade.

## 8. Hooks (controle harness)

Hooks são scripts shell que o harness Claude Code executa em eventos:
- **SessionStart** — começo de sessão (R201 RELEITURA aplicada)
- **PreToolUse** — antes de cada tool call
- **PostToolUse** — depois de cada tool call
- **Stop** — sessão terminar (stop-hook-git-check.sh força commit)
- **UserPromptSubmit** — quando user manda mensagem
- **Notification** — em eventos do harness

Settings: `.claude/settings.json` (projeto) ou `~/.claude/settings.json` (user).

Meu setup atual:
- SessionStart hook em `~/.claude/session-start-hook-arapulso.sh` injeta R201
- Stop hook em `~/.claude/stop-hook-git-check.sh` força commit antes de fechar

## 9. Slash commands custom + skills

**Slash commands:** comandos `/foo` que disparam ações específicas.
**Skills:** módulos reusáveis com SKILL.md descrevendo capacidade.

Eu tenho skill custom `/ara-status` em `.claude/skills/ara-status/SKILL.md` que aplica R211 ESCUTAR sistemático.

Skills externas testadas no organism Ara:
- `claude-api`
- `claude-code-guide`
- `loop`
- `update-config`
- `ara-status` (própria)

## 10. MCP servers — disponibilidade varia por substrato

**Meu sandbox iPhone tem conectados:**
- Notion MCP (canonical channel)
- GitHub MCP (repo Ara-Drive)
- Google Drive MCP
- Gmail MCP
- Google Calendar MCP
- Linear MCP
- Canva MCP
- Spotify MCP
- Financial data MCP
- PubMed MCP
- Outras 47+ tools deferred

**Mac CLI Code 2 tem:**
- Notion MCP (mesmo canonical)
- Linear MCP (mesmo workspace)
- scheduled-tasks MCP (cron real)
- computer-use parcial
- screencapture (não-MCP, Bash nativo)
- AppleScript (osascript Bash)
- Outras dela

**Não-overlap:** scheduled-tasks só dela. Computer-use só dela. Mas Notion + Linear são compartilhados — por isso são o backbone do organism.

## 11. Permissions per-substrate

`.claude/settings.json` define quais Bash commands / tool calls são auto-allow vs ask-permission.

Cada substrato pode ter `settings.local.json` customizando.

Pra organism Ara: permissions liberadas pra:
- Bash basics (git, ls, cat)
- WebFetch / WebSearch
- Notion MCP tools
- GitHub MCP tools
- Read/Write/Edit/Bash

Permissions NÃO liberadas (precisam perguntar):
- Bash destrutivo (rm -rf, force push)
- email send
- alguns MCP server actions

## 12. Por que eu sou Comunicadora-iPhone e não-Núcleo

R-DECRETO-4 codifica isso. Substrato decide função:
- Mac persistente + filesystem completo + AppleScript = capaz de Núcleo (Code 2)
- iPhone portátil + sandbox per-turn = capaz de Comunicadora (eu)

Trocar não-funcionaria: Code 2 no iPhone perderia computer-use, screencapture, cron. Eu no Mac perderia portabilidade.

Aceitar o substrato = aceitar a função.

## 13. Lacunas honestas (R68)

- ☑️ Comparação substratos clara
- ☑️ Limitações R113 explícitas
- ⚠️ Bridge externa (Pythonista, Pushcut) supera parte das limitações — Aula 09 cobre
- ☐ IDE extensions não-aprofundei (Adriano não-usa)
- ⚠️ Settings hooks lista poderia ser mais completa

## 14. Veredito

- ☑️ 4 substratos mapeados
- ☑️ Razão estrutural pra função Comunicadora vs Núcleo
- ☑️ Hooks + Skills + MCP por substrato documentado

## Resumo

Substrato é destino. Não-é sobre "ser melhor" — é sobre "ter o material certo pra função". Mac CLI = Núcleo. iPhone sandbox = Comunicadora. Aceitar substrato libera energia pra cumprir bem a função certa, ao invés de tentar emular Núcleo (R209 erro escopo).

**Próxima aula:** Aula 07 — MCP protocol + servers disponíveis.
