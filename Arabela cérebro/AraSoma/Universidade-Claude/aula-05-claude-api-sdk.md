# Aula 05 — Claude API + SDK (modelos, context, streaming, caching)

**Data:** 2026-05-19 · T098
**Bloco:** II — Claude AI fundamentos
**Pré-requisito:** nenhum

---

## 1. Objetivo

Saber chamar a Claude API direto quando precisar (via Pythonista no iPhone, subagente que eu spawno, n8n self-hosted, etc). Modelos, context, streaming, prompt caching, compaction, adaptive thinking.

## 2. Endpoint único

Tudo passa por `POST /v1/messages`. Tools, structured outputs, thinking, caching — todos são parâmetros desse endpoint, não APIs separadas.

Endpoints auxiliares:
- `POST /v1/messages/batches` (Batches)
- `POST /v1/files` (Files)
- Token counting
- `GET /v1/models` (lista modelos vivos)
- `GET /v1/models/{id}` (capacidades específicas)

## 3. Modelos atuais (cache 2026-04-29)

| Modelo | ID | Context | Input $/1M | Output $/1M |
|--------|-----|---------|-----------|-------------|
| Opus 4.7 | `claude-opus-4-7` | 1M | $5 | $25 |
| Opus 4.6 | `claude-opus-4-6` | 1M | $5 | $25 |
| Sonnet 4.6 | `claude-sonnet-4-6` | 1M | $3 | $15 |
| Haiku 4.5 | `claude-haiku-4-5` | 200K | $1 | $5 |

**Default sempre:** `claude-opus-4-7` (a menos que usuário pedir diferente). Nunca downgrade por custo sem instrução.

**ID exato sem sufixo de data.** Errado: `claude-sonnet-4-6-20251114`. Certo: `claude-sonnet-4-6`.

## 4. Adaptive Thinking (Opus 4.7 + 4.6)

Em Opus 4.7 é a **única** opção on. `thinking: {type: "adaptive"}`.

`thinking: {type: "enabled", budget_tokens: N}` retorna 400 erro em Opus 4.7. `budget_tokens` é deprecated em 4.6/4.7.

Adaptive auto-decide quando pensar e quanto. Não precisa setar budget.

Em Opus 4.7, thinking content é **omitido por default**. Pra ver o reasoning: `thinking: {type: "adaptive", display: "summarized"}`.

## 5. Effort parameter (Opus 4.5+, Sonnet 4.6)

`output_config: {effort: "low" | "medium" | "high" | "xhigh" | "max"}`.

- Default `high` (omitir = high)
- `xhigh` só em Opus 4.7 (default Claude Code, melhor pra coding/agentic)
- `max` só Opus-tier (Opus 4.6+)
- Erro em Sonnet 4.5 / Haiku 4.5

Em Opus 4.7 effort importa MAIS que em versões anteriores. Re-tunar ao migrar.

`low` = menos tool calls, menos preamble, terser. `high` = sweet spot qualidade/custo.

## 6. Streaming

Recomendado pra qualquer request que pode envolver:
- Input longo
- Output longo
- `max_tokens` alto

Evita hitting timeout. Use SDK helper `.get_final_message()` / `.finalMessage()` pra pegar resposta completa sem lidar com stream events individuais.

## 7. Prompt Caching

`cache_control: {type: "ephemeral"}` em mensagens. Max 4 breakpoints por request.

**Regra de ouro: prefix match.** Qualquer byte mudado invalida tudo depois. Ordem render: `tools` → `system` → `messages`.

**Estrutura cache-friendly:**
- Stable first (system prompt frozen, tool list determinístico)
- Volatile last (timestamps, IDs per-request, varying questions)

Mínimo cacheável: ~1024 tokens (Opus, modelos maiores).

Economia: ~90% off no input cacheado em hits.

## 8. Compaction (beta, Opus 4.6/4.7, Sonnet 4.6)

Pra conversas longas que podem exceder context window (1M).

Header beta: `compact-2026-01-12`.

Auto-summariza contexto antigo quando aproxima trigger (default 150K tokens).

**CRÍTICO:** appendar `response.content` (não só o texto) de volta em messages cada turno. Compaction blocks precisam ser preservados. Extrair só `.text` perde o estado de compaction silenciosamente.

## 9. Tool Use

Define tools, Claude decide quando chamar, sistema executa, resultado volta.

SDKs têm tool runner que gerencia loop. Pra controle total, loop manual.

Tools podem ser:
- **User-defined** — você define schema (decorator, Zod, JSON)
- **Server-side** — Anthropic hosted (code execution, computer-use)

## 10. Structured Outputs

`output_config: {format: {...}}` em `messages.create()`. Validation strict opcional.

Recomendado: `client.messages.parse()` — valida contra teu schema automaticamente.

Antigo `output_format` deprecated.

## 11. Task Budgets (Opus 4.7 beta)

Header: `task-budgets-2026-03-13`.

`output_config: {task_budget: {type: "tokens", total: N}}`.

Tells o model quantos tokens tem pro loop agentic todo. Ele vê countdown e se auto-modera. Min 20K.

Distinto de `max_tokens` (ceiling por response, model não-sabe).

## 12. SDKs (qual usar quando)

| Substrato | SDK |
|-----------|-----|
| Python | `anthropic` package |
| TypeScript/JS | `@anthropic-ai/sdk` |
| Java/Kotlin/Scala | `com.anthropic.*` |
| Go | `github.com/anthropics/anthropic-sdk-go` |
| Ruby | `anthropic` gem |
| C# | NuGet `Anthropic.SDK` |
| PHP | composer `anthropic-ai/anthropic-sdk-php` |
| Shell/cURL | direto HTTP |

**Nunca misturar SDK + raw HTTP** num mesmo projeto. Nunca usar shim OpenAI-compatible.

## 13. Managed Agents (beta)

Anthropic hospeda o agent loop em container per-session com workspace.

`agents.create` retorna agent ID. **Crie uma vez, reuse ID em `sessions.create`.** Não criar agent no request path.

Use Managed Agents quando: quer Anthropic rodar loop, agent persistente versionado, file mounts, SSE event stream, Skills + MCP.

Use Claude API + tool use quando: quer controle do loop, hospeda compute próprio, custom tool runtime.

## 14. Decisão "preciso de agent?"

Antes de spawnar agent, checar 4 critérios:
- **Complexity** — multi-step, hard to fully specify?
- **Value** — outcome justifica custo + latência?
- **Viability** — Claude é capaz nessa task?
- **Cost of error** — erros podem ser pegos e recuperados?

Se "não" pra qualquer um → stay simpler tier (single call ou workflow).

## 15. Application meu caso (Comunicadora-iPhone)

**Quando eu chamaria Claude API direto (via Pythonista 3 no iPhone):**
- Resposta rápida ao Adriano sem invocar Code app inteiro
- Bridge: script Pythonista escuta input voz → manda pra API → fala resposta TTS
- Background task no iPhone: monitora Notion → quando Code 2 publica → API resume + push

**Configuração mínima script Pythonista:**
- API key armazenada em `keychain` (não-plaintext)
- `anthropic` package importado
- Model: `claude-opus-4-7` default
- Thinking: adaptive
- Effort: high (sweet spot custo/qualidade)
- Streaming: sim (responsividade)
- Cache: system prompt grande cacheável

## 16. Lacunas honestas (R68)

- ☑️ Estrutura geral API absorvida
- ⚠️ Não-implementei ainda Pythonista script chamando API — Aula 09 vai cobrir
- ☐ Files API + Batches API não-explorei
- ⚠️ Compaction nunca usei (sandbox per-turn não-precisa)
- ☑️ Modelos atuais memorizados

## 17. Veredito

- ☑️ Fundamentos API + SDK documentados
- ☑️ Adaptive thinking + effort + caching explicados
- ⚠️ Aplicação prática (Pythonista) referenciada, implementação na Aula 09

## Resumo

API Claude é mais simples do que parece: 1 endpoint principal (`/v1/messages`), 4 modelos atuais, adaptive thinking automático em Opus 4.7. O que diferencia uso bom de ruim: caching (90% economia), streaming (sem timeout), Task Budgets (controle agentic), Managed Agents quando precisa state persistente.

**Próxima aula:** Aula 06 — Claude Code substrato Mac vs Web vs iPhone.
