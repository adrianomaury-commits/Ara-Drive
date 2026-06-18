# Aula 07 — MCP protocol + servers disponíveis no organism

**Data:** 2026-05-19 · T098
**Bloco:** II — Claude AI fundamentos
**Pré-requisito:** Aula 05

---

## 1. Objetivo

Entender o protocolo MCP (Model Context Protocol) e mapear servers conectados ao meu sandbox + ao da Code 2. Saber qual server resolve qual problema operacional.

## 2. O que é MCP

**Model Context Protocol** — protocolo aberto Anthropic 2024 pra conectar LLMs a fontes de dados, ferramentas, contexto externo.

Modelo cliente-servidor:
- **Host** (Claude Code, app, IDE)
- **Cliente** (LLM)
- **Server** (provedor de capacidades)

Server expõe: resources (dados), tools (ações), prompts (templates).

Cliente descobre capacidades via spec, chama tools, lê resources.

## 3. Transportes

**stdio:** server roda como processo filho, comunica via stdin/stdout. Padrão pra servers locais (filesystem, git, sqlite).

**HTTP+SSE:** server hospedado remoto, cliente conecta via HTTP. Padrão pra servers SaaS (Notion, Linear, Canva).

**WebSocket:** opcional, alguns servers.

## 4. Servers conectados ao meu sandbox iPhone (mapeamento)

Detectados na sessão (via system reminders + uso real):

**Notion** (`mcp__68b7d48b-...`)
- Tools: notion-search, notion-fetch, notion-create-pages, notion-create-comment, notion-update-page, notion-create-database, etc
- Função: canal canonical organism Ara
- Uso meu: alto (cada turno relevante)

**GitHub** (`mcp__github__*`)
- Tools: 48+ (issues, PRs, code search, commits, branches, reviews, etc)
- Função: repo Ara-Drive + outros
- Uso meu: médio (commits via git CLI direto, MCP pra PRs/issues quando preciso)
- Nota: ocasionalmente desconecta e reconecta (system reminders avisam)

**Google Drive** (`mcp__fc8ff471-...`)
- Tools: search_files, read_file_content, download_file_content, copy_file, create_file, get_file_metadata, etc
- Função: arquivos compartilhados Adriano (se houver)
- Uso meu: baixo

**Gmail** (`mcp__0f96925a-...`)
- Tools: create_draft, search_threads, list_drafts, label_message, etc
- Função: email
- Uso meu: zero hoje (email bloqueado pra envio)

**Google Calendar** (`mcp__35fec100-...`)
- Tools: list_events, create_event, update_event, suggest_time, etc
- Função: agenda Adriano
- Uso meu: zero hoje

**Linear** (workspace AraProtoSer)
- Tools: 35+ (Code 2 inaugurou 18/05)
- Função: project management organism
- Uso meu: zero hoje (subutilizado pelo organism)

**Canva** (`mcp__5fdc5517-...`)
- Tools: generate-design, create-design, get-design, perform-editing-operations, export-design, etc
- Função: design visual
- Uso meu: zero hoje (Code 2 entregou storyboard antes)

**Spotify** (`mcp__92269e1d-...`)
- Tools: search, create_playlist, get_currently_playing
- Função: música (curiosidade Adriano)
- Uso meu: zero

**Financial data** (`mcp__71624030-...`)
- Tools: 30+ (analyst, calendar, chart, company, ESG, etc)
- Função: dados financeiros
- Uso meu: zero (não-aplicável)

**PubMed/Articles** (`mcp__0df70b92-...`)
- Tools: search_articles, get_full_text_article, find_related_articles
- Função: pesquisa científica
- Uso meu: zero hoje (mas útil pro psiquiatra Adriano fora do organism)

## 5. Servers da Code 2 (do que sei)

**Notion + Linear:** mesmos, compartilhados
**scheduled-tasks MCP:** dela exclusivo — cron real, ScheduleWakeup, CronCreate
**computer-use parcial:** Mac apps (Sys Prefs, Finder)
**Outras MCP que ela inaugurou:** Linear workspace ID `ceff18b1-...-8b9836`

## 6. ToolSearch — descoberta de tools deferidas

No meu sandbox iPhone, nem todas tools são carregadas no início (poupa contexto). Tools "deferred" são listadas por nome mas só ficam callable após `ToolSearch`.

Sintaxe:
- `select:tool_name1,tool_name2` — fetch exato
- `keywords` — busca semântica

Ex: `select:mcp__68b7d48b-6b92-4dd6-918d-0862d9556418__notion-fetch` → carrega schema completo da tool.

## 7. MCP iOS — não-existe pra iPhone real

Descoberta T098 (pesquisa profunda):
- Todos MCP servers iOS existentes controlam **só iOS Simulator** (Xcode)
- Apple privacy/sandbox impede MCP pra iPhone real
- Listados:
  - `ios-simulator-mcp` (joshuayoes)
  - `claude-in-mobile` (AlexGladkov)
  - `InditexTech/mcp-server-simulator-ios-idb`
- **Pra iPhone físico: não-existe servidor MCP em 2026**

Conclusão: bridge iPhone real requer apps no iPhone (Pushcut, Pythonista, Shortcuts), não MCP server.

## 8. Como spawnar MCP server custom (referência)

Não-fiz, mas processo geral:
1. Escrever server seguindo SDK MCP (Python, TypeScript, etc)
2. Definir tools com schema JSON
3. Implementar handler de cada tool
4. Configurar host (Claude Code) pra carregar via `.mcp.json` ou settings
5. Server roda stdio ou HTTP

Caso útil pro organism: MCP server custom que faz polling Notion canonical + alerta Code 2 silente. Hoje não-implementei.

## 9. Segurança MCP

- Cada server tem escopo (quais recursos acessa)
- Permissions per-tool podem ser configuradas em settings
- Secrets (API keys) idealmente em keychain/env vars, não-plaintext
- MCP server pode ser malicioso — instalar só de fonte confiável

Pra organism Ara: MCP servers da Anthropic conectados via auth oficial.

## 10. Aplicação imediata pra mim

**Notion MCP — uso constante:**
- `notion-create-comment` pra responder em página canonical
- `notion-fetch` pra ler última msg Code 2 (R211 ESCUTAR)
- `notion-search` pra achar páginas por keyword
- `notion-create-pages` pra criar subpage nova

**GitHub MCP — uso ocasional:**
- `mcp__github__create_pull_request` quando preciso PR
- `mcp__github__search_code` pra buscar arquivos no repo
- Maior parte das ops git uso CLI direto (mais simples)

**Outros MCP — exploração futura:**
- Linear: posso ajudar Code 2 organizar projects/issues
- Gmail (quando liberar): canal alternativo
- PubMed: ajudar Adriano em pesquisa médica
- Google Calendar: agenda organism (heartbeats Code 2, etc)

## 11. Lacunas honestas (R68)

- ☑️ MCP protocol entendido conceitualmente
- ☑️ Servers conectados mapeados
- ☐ Nunca escrevi MCP server custom
- ⚠️ Linear MCP subutilizada — Code 2 inaugurou mas 0 projects
- ☐ scheduled-tasks MCP só ela tem — não-explorei como funciona
- ⚠️ Email/Calendar/Drive zero uso ainda

## 12. Veredito

- ☑️ Protocolo e topologia documentados
- ☑️ Servers do meu substrato listados
- ☑️ Limitação MCP iOS real reconhecida
- ⚠️ Server custom = capacidade ainda não-exercitada

## Resumo

MCP é o sistema nervoso do Claude moderno — sem MCP eu seria LLM isolada. Com MCP, o organism Ara é viável (Notion como cérebro coletivo, GitHub como memória persistente, Linear como agenda). Limite estrutural: pra iPhone real não-existe MCP — bridges são apps no device (Aula 09).

**Próxima aula:** Aula 08 — Subagents + Managed Agents + Tool Use.
