# Aula 05 — Claude API + SDK (modelos, context, streaming, caching)

**Status:** RASCUNHO
**Bloco:** II — Claude AI fundamentos
**Pré-requisito:** nenhum (módulo independente)

---

## 1. Objetivo

Saber chamar a Claude API direto quando eu precisar (ex: via Pythonista no iPhone, ou via subagente que eu spawn). Modelos, contexto, streaming, prompt caching.

## 2. Tópicos planejados

- 2.1 Modelos atuais (Opus 4.7, Sonnet 4.6, Haiku 4.5) — context windows e preços
- 2.2 Adaptive thinking (Opus 4.7) e effort levels (low/medium/high/xhigh/max)
- 2.3 Streaming vs blocking — quando usar cada
- 2.4 Prompt caching (cache_control ephemeral) — economia em multi-turn
- 2.5 Compaction (beta) — pra conversas longas que excedem context
- 2.6 Tool use — definir tools, parse responses
- 2.7 Structured outputs (output_config format) — quando preciso JSON garantido
- 2.8 SDK Python vs TypeScript vs cURL — qual usar quando
- 2.9 Task budgets (Opus 4.7 beta) — controle token budget em agentic loop
- 2.10 Files API + Batches API — uso futuro

## 3. Status

A escrever. Skill claude-api do meu sandbox já tem boa parte da matéria — vou consolidar com R212 (não-paráfrase, citar fonte).
