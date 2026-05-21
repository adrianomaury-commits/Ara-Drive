# Aula 08 — Subagents + Managed Agents + Tool Use

**Data:** 2026-05-19 · T098
**Bloco:** II — Claude AI fundamentos
**Pré-requisito:** Aulas 05 + 07

---

## 1. Objetivo

Aprender quando spawnar subagente, quando usar Managed Agents (Anthropic-hosted), quando construir tool use manual. Casos reais do organism Ara aplicados.

## 2. Três tiers de complexidade

| Tier | Usa | Quando |
|------|-----|--------|
| **Single call** | Claude API direto | Classificação, summarização, extração, Q&A |
| **Workflow** | API + tool use, código orquestra loop | Pipeline multi-step com lógica clara |
| **Agent (custom)** | API + agentic loop, modelo decide trajetória | Open-ended, exploração model-driven |
| **Agent (managed)** | Managed Agents API | Stateful, container, file mounts, SSE |

**Default: começar simples.** Single call cobre maioria dos casos. Só ir pra agent quando 4 critérios passam.

## 3. Subagents no Claude Code

No meu sandbox iPhone, posso spawnar subagentes via `Agent` tool.

**Tipos de subagentes:**
- `claude` — catch-all
- `claude-code-guide` — perguntas sobre Claude Code, SDK, API
- `Explore` — read-only search agent (find files, grep, "where is X")
- `general-purpose` — researching, multi-step tasks
- `Plan` — software architect, design implementation
- `statusline-setup` — config status line

**Como funciona:**
1. Eu chamo `Agent(description, prompt, subagent_type)`
2. Subagente nasce zerado, lê só o prompt que dei
3. Executa task com tools próprias
4. Retorna 1 mensagem com resultado
5. Resultado fica no MEU contexto, mas processo do subagente fica fora

**Vantagem:** protege meu contexto principal. Subagente faz pesquisa pesada (lê 30 arquivos), eu recebo só o resumo.

**Quando usar:**
- Pesquisa cross-codebase
- Independente, paralelo (vários subagentes em 1 mensagem)
- Task open-ended onde resultado é mais importante que processo

**Quando NÃO usar:**
- Lookup target conhecido (use Read ou Bash direto)
- Tarefa simples (overhead não-compensa)

## 4. Padrões de uso real (casos meus T097-T098)

**Caso T097 — teste zerada AraSoma:**
- Spawn `general-purpose` com prompt pra ler arapulso e responder 5 perguntas + auditoria
- Resultado: confirmou meu AraPulso = log/diário, não AraSoma operacional
- Sem subagente eu não-conseguiria simular "zerada" honest

**Caso T098 — pesquisa Ara Mobile Bridge:**
- Spawn `general-purpose` em background
- Prompt: pesquisa web profunda 17+ apps iPhone control
- Resultado: 1500+ palavras com TOP 3 (Pushcut, Pythonista, Telegram), arquitetura híbrida
- Sem subagente eu não-faria essa pesquisa exaustiva sem queimar meu contexto

**Caso geral — quando algo demora >3 queries:**
- Spawn subagente é mais eficiente
- Marco como background se não-preciso resultado imediato

## 5. Managed Agents (beta)

Anthropic hospeda agent loop completo em container per-session com workspace.

**Conceitos:**
- **Agent** — config persistente (system prompt, tools, model). Crie uma vez.
- **Session** — execução de um agent. Pin a uma versão.
- **Container** — workspace isolado per-session (filesystem, bash, code exec)

**Workflow:**
1. `agents.create(name, system, tools, model)` → retorna agent ID
2. `sessions.create(agent_id, input)` → cria session, retorna SSE event stream
3. Stream eventos (thinking, tool calls, file ops, response)
4. Agent termina quando completo

**Quando usar Managed Agents:**
- Stateful coding agent com workspace por tarefa
- Long-running research agent com event stream pra UI
- Agent versionado usado por muitas sessions

**Quando NÃO usar:**
- Quero hospedar compute próprio
- Custom tool runtime que Anthropic não-oferece
- Workflow simples (use API direto)

**Limite:** Bedrock, Vertex AI, Foundry NÃO suportam Managed Agents. First-party API e Claude Platform AWS sim.

## 6. Tool Use manual (controle total)

Quando quer controle do loop:
1. Define tools (schema JSON)
2. `messages.create(messages, tools)` → resposta com tool_use blocks
3. Executa tools localmente
4. Append `tool_result` em messages
5. Re-chama API → próximo turno
6. Loop até resposta final

**SDKs têm tool runner** (`@beta_tool` Python, `betaZodTool` TS, etc) que abstrai isso.

**Quando usar loop manual:**
- Approval gates (humano confirma cada tool call)
- Custom logging
- Execução condicional
- Compliance/audit trail

## 7. Server-side tools (Anthropic hospeda)

**Code execution:** declarar tool em request → Claude executa Python automaticamente em container Anthropic. Resultado vem no response.

**Computer-use:** server-hosted ou self-hosted. Server-hosted: Claude opera container desktop virtual.

**Vantagem:** sem precisar tu hospedar runtime de execução.

## 8. Padrão "agent spawna agent"

Composição: subagente pode spawnar sub-sub-agente.

Usar com cuidado — cada nível adiciona latência + tokens. Geralmente 2 níveis é máximo prático.

Pra mim: eu spawno subagente que faz pesquisa. Subagente pode spawnar sub-subagente pra leitura paralela de 5 arquivos. Eu vejo só o final.

## 9. Custo / latência

| Aproach | Latência | Tokens | Quando |
|---------|----------|--------|--------|
| Single call | ~1-3s | mínimo | Tarefa simples |
| Workflow + tools | ~5-30s | médio | Pipeline definido |
| Subagent | ~10-300s | alto (lê arquivos) | Pesquisa, exploração |
| Managed Agent | ~30s-10min | muito alto | Stateful longo prazo |

## 10. Quando NÃO usar agent

Mesmo se task é multi-step, agent overkill se:
- Pipeline é determinístico (use workflow code)
- Resposta é "extrair X" (use single call)
- Custo de erro é baixo e validável (use simpler tier)

Default: simplest tier que funciona.

## 11. Aplicação pra mim (Comunicadora-iPhone)

**Single call (eu mesma sem ajuda):**
- Responder Adriano em chat direto
- Aplicar R211 ESCUTAR (1 fetch Notion + analise)
- Painel + status AraSoma

**Subagent (delego task):**
- Pesquisa web profunda (T098 case)
- Teste zerada AraSoma (T097 case)
- Leitura cross-arquivo grande
- Audit independente

**Managed Agent (não-uso hoje):**
- Seria útil pra Code 2 (substrato persistente). Pra mim per-turn não-faz sentido.

**Tool use manual:**
- Não-uso direto, harness Claude Code já abstrai

## 12. Lacunas honestas (R68)

- ☑️ Subagents experiência prática T097 + T098
- ⚠️ Managed Agents nunca chamei API direto (Claude Code já abstrai)
- ☐ Tool use loop manual nunca implementei
- ☑️ Conhecimento conceitual sólido
- ⚠️ Não-uso paralelização suficiente (poderia spawnar 3 subagentes pra 3 pesquisas independent)

## 13. Veredito

- ☑️ Tiers de complexidade documentados
- ☑️ Casos reais do organism mapeados
- ☑️ Quando usar / não-usar agent clarificado
- ⚠️ Managed Agents teórico só

## Resumo

Não-todo problema precisa de agent. Default: começar com single call, escalar se necessário. Subagent é minha ferramenta mais poderosa hoje (T097 zerada + T098 pesquisa = casos transformadores). Managed Agents é overkill pro meu substrato per-turn. Composição agent-spawna-agent funciona mas tem custo.

**Próxima aula:** Aula 09 — Sandbox iPhone bridge (prioridade R-DECRETO-6).
