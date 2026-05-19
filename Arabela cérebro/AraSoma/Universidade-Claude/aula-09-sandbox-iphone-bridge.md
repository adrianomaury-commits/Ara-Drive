# Aula 09 — Sandbox iPhone per-turn limites + Pythonista/Shortcuts/Pushcut bridge

**Status:** RASCUNHO
**Bloco:** III — Sandbox + workarounds
**Pré-requisito:** Aulas 06 + 07

---

## 1. Objetivo

Documentar EXATAMENTE o que minha sandbox iPhone pode e não-pode fazer, e como construir a ponte (bridge) externa pra superar limites (Pythonista, Shortcuts, Pushcut, Telegram).

## 2. Tópicos planejados

- 2.1 Arquitetura sandbox per-turn — container Linux Anthropic remoto
- 2.2 O que TEM no sandbox (Bash, Read, Write, MCP servers, git, python, node)
- 2.3 O que NÃO-TEM (filesystem Mac/iPhone, screencapture, clipboard iOS, cron persistente)
- 2.4 R113 — não-CronCreate, sem ScheduleWakeup
- 2.5 Bridge externa Pushcut — webhook → notification iPhone acionável
- 2.6 Bridge Pythonista 3 — Python local no iPhone, módulos clipboard/notification/photos
- 2.7 Bridge Shortcuts — iOS Atalhos com HTTP POST + Siri trigger
- 2.8 Bridge Telegram Bot — canal bidirecional grátis
- 2.9 Bridge git Ara-Drive — Working Copy clone no iPhone
- 2.10 Arquitetura híbrida proposta (T098 pesquisa)
- 2.11 Implementação MVP (Pushcut + Shortcuts + Telegram)

## 3. Status

A escrever. Pesquisa T098 (Aula T098) já consolidou a maioria desse conteúdo — vou referenciar e expandir.
