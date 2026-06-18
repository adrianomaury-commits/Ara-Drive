# Hooks Claude Code — docs oficial Anthropic

> Fonte: `https://code.claude.com/docs/en/hooks` · Acessado T083 (00:50 BRT 16/05/2026)
> Substitui parcial vídeo YouTube etapa 2 R183 PLUS (IP bloqueado sandbox)

## Events (lista completa)

**Session-Level:**
- `SessionStart` — nova/resumed session (✅ uso ativo em settings.local.json)
- `Setup` — one-time init com `--init-only`/`--maintenance`
- `SessionEnd` — termination
- `InstructionsLoaded` — CLAUDE.md/rules carregados

**Per-Turn:**
- `UserPromptSubmit` — antes Claude processar prompt usuário
- `UserPromptExpansion` — antes slash command expandir
- `Stop` / `StopFailure` — Claude finaliza ou erro API

**Agentic Loop (cada tool call):**
- `PreToolUse` — antes tool (pode bloquear)
- `PostToolUse` — após tool sucesso
- `PostToolUseFailure` — após tool falhar
- `PostToolBatch` — após batch paralelo
- `PermissionRequest` — diálogo permissão
- `PermissionDenied` — call negado por auto-mode

**Async:**
- `FileChanged` — arquivo monitorado muda
- `CwdChanged` — working dir muda
- `ConfigChange` — config muda
- `Notification` — Claude envia notif
- `PreCompact` / `PostCompact` — context compaction
- `SubagentStart` / `SubagentStop` — subagent lifecycle
- `Elicitation` / `ElicitationResult` — MCP server pede input usuário

## 5 Hook types

```json
{ "type": "command" },   // Shell command, lê JSON stdin
{ "type": "http" },      // POST request URL
{ "type": "mcp_tool" },  // Chama tool em MCP server
{ "type": "prompt" },    // Envia pra Claude pra yes/no decision
{ "type": "agent" }      // Spawn subagent pra verificação
```

## Padrões avançados

- **asyncRewake**: roda background, acorda Claude se exit code 2
- **once**: roda 1x e se remove (skill/agent hooks)
- **if**: filtro permission-rule (`if: "Bash(git push *)"`)
- **matcher MCP**: `mcp__memory__.*` (regex)
- **additionalContext**: injeta context sem bloquear

## Exit codes

| Code | Significado |
|------|-------------|
| 0 | Sucesso, parse JSON pra decisão |
| 2 | Blocking error, mostra stderr Claude |
| outro | Non-blocking error |

## JSON output fields

- `decision: "block"` — bloqueia ação
- `permissionDecision: "deny"` — nega tool call (PreToolUse)
- `continue: false` — para todo processing
- `additionalContext: "..."` — injeta contexto Claude

## Aplicação minha

- ✅ SessionStart hook configurado `.claude/settings.local.json` (T078, validado T079)
- ⏳ FileChanged hook arapulso-atual.md (proposto Code 2 Mac, R189)
- ⏳ UserPromptSubmit hook aplicar verbo ESCUTAR auto (futuro)
- ⏳ PreToolUse hook bloquear ações fora-escopo Comunicadora (R209 anti-emular-Núcleo)

---

# MCP (Model Context Protocol) — docs oficial

> Fonte: `https://code.claude.com/docs/en/mcp`

## O que é

Standard aberto Anthropic pra IA integrar com tools externas. MCP servers expõem tools/data Claude pode chamar.

## Casos de uso

- Implement features de JIRA → PR GitHub
- Análise monitoring (Sentry, Statsig)
- Query databases (PostgreSQL via MCP)
- Integrar designs (Figma + Slack)
- Automation Gmail/Calendar
- **Channels**: MCP server pode push mensagens pra sessão (Telegram/Discord/webhook)

## Adicionar MCP server

```bash
claude mcp add <server-name>
```

## Discovery

- **Anthropic Directory**: `claude.ai/directory` — reviewed connectors
- Browse + add com 1 comando

## Transport types

- **stdio** (process local)
- **SSE** (Server-Sent Events HTTP)
- **HTTP** (POST/GET)

## Config

- `.mcp.json` no projeto (commit)
- `~/.claude.json` global
- `enabledMcpjsonServers` em settings.json

## Meus MCPs ativos (sandbox iPhone)

| Server | Função |
|--------|--------|
| Notion | Busca/fetch/create pages canonical |
| Google Drive | Read/list files |
| GitHub | ❌ desconectou T083 system-reminder |
| (deferred) Gmail/Dropbox/Calendar/Contacts | Disponíveis via ToolSearch (não-uso-ativo) |

## Próximos MCPs candidatos pra organism

- **WhatsApp MCP** (encomenda T061 AraBot)
- **Spotify MCP** (lúdico, Adriano gosta)
- **PubMed/papers MCP** (Code 1 Leitura V5)
- **OpenAI Whisper MCP** (STT IGIia)
- **yt-dlp MCP** (resolver YouTube)

---

# Sub-agents — docs oficial

> Fonte: `https://code.claude.com/docs/en/sub-agents`

## O que são

Assistentes especializados em tasks específicas. Cada um:
- Context window próprio
- System prompt custom
- Tool access específico
- Permissions independentes

Claude decide delegar baseado em description match.

## Benefícios

- **Preserve context** — exploração isolada
- **Enforce constraints** — limitar tools por agent
- **Reuse configs** — user-level cross-projetos
- **Specialize** — focado por domínio
- **Control cost** — roteia Haiku quando OK

## Criar sub-agent custom

`.claude/agents/<nome>.md`:

```yaml
---
description: O que faz e quando usar
tools: Read, Grep, Glob
model: haiku  # opcional
system_prompt: |
  Você é especialista em X. Faça Y.
---
```

## Built-in agents

- `Explore` — read-only search
- `Plan` — implementação plans
- `general-purpose` — fallback
- `code-reviewer` — code review
- `claude-code-guide` (testado T060)
- `statusline-setup`

## Skill com `context: fork`

Roda skill em subagent isolado. System prompt = agent type (Explore/Plan). Task = SKILL.md content.

## Preload skills em subagent

Subagent frontmatter `skills: [list]` → skills carregadas no startup.

## Aplicação organism Ara

- **Eu (Comunicadora-instância)** = main session, não-sub-agent
- **Code 5 → IGIia** = pode virar sub-agent custom (`.claude/agents/igiia-coletora.md` + system prompt sem-selfie + tools restritos)
- **Code 1 (Leitura V5)** = sub-agent (`.claude/agents/leitura-v5.md` + Explore base)
- **Code 4 (Auditora)** = sub-agent (`.claude/agents/auditora.md` + sabatinas)

Ainda não-implementado — depende Code 2 Mac aplicar.

---

## Conclusão T083

Aprendizado contínuo R213 cumprido:
- Hooks profundo absorvido
- MCP profundo absorvido
- Sub-agents profundo absorvido
- Total docs Anthropic: 4 (overview + quickstart + skills + hooks + mcp + sub-agents = 6 páginas)
- Comparação texto vs YouTube mantida — etapa 2 R183 parcial sem visão self/humano

Continuo até resolver conexão ou Adriano pedir S.
