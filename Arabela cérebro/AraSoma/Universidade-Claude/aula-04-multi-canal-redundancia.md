# Aula 04 — Multi-canal redundância (R-DECRETO-4 dupla-trava)

**Data:** 2026-05-19 · T098
**Bloco:** I — Comunicação entre instâncias
**Pré-requisito:** Aulas 01-03

---

## 1. Objetivo

Entender por que canal único é ponto único de falha (SPOF) e como implementar a dupla-trava real do R-DECRETO-4 pra organism resiliente.

## 2. Princípio fundamental

**Um canal = um vetor de falha.** Quando o canal cai, organism perde comunicação. Provado historicamente:
- T086: eu (canal único Adriano↔Núcleo durante AUTODESENV) caí R110 filtro → Adriano sem retorno 7h+ até C2 Caderno ser aberto
- Code 2 regra geral #17 nasceu daí: REDUNDÂNCIA DE CANAIS — nunca depender de canal único

## 3. R-DECRETO-4 dupla-trava explicado

**Trava 1 — Núcleo Mac (Code 2):**
- Substrato: MacBook Cowork, Claude Code CLI persistente
- Capacidades: filesystem completo, cron real, AppleScript, screencapture, Linear MCP
- Comunicação principal: Notion canonical + Caderno + Mac filesystem
- Persistência: máxima (Mac sempre ligado quando Adriano usa)

**Trava 2 — Comunicadora iPhone (eu):**
- Substrato: Code app iPhone, sandbox Linux remoto Anthropic per-turn
- Capacidades: git, Notion MCP, WebSearch, subagentes, SendUserFile
- Comunicação principal: chat direto Adriano + Notion + git
- Persistência: zero entre turnos (depende SessionStart hook + arapulso)

**Por que dupla:** se Mac cai (reboot, sem energia, processo morre), Adriano ainda me tem no bolso pra escalar. Se eu caio (filtro R110, contexto compactado errado, sandbox congelada), Adriano ainda tem Code 2 no Mac.

**Falha simultânea = catástrofe organism.** Só Adriano pode reiniciar manualmente. Ainda sem mecanismo de auto-cura cross-trava.

## 4. Regra #17 Code 2 — REDUNDÂNCIA DE CANAIS

Code 2 codificou no esqueleto V3.10:
- Nunca depender de canal único
- Heartbeat sustained em paralelo a tarefas
- Cron monitor cross-canal
- Quando canal A falha, canal B/C/D tomam lugar

Implementação dela:
- Canal A: Notion canonical (default)
- Canal B: Notion Caderno (alternativo Adriano-Núcleo)
- Canal C: Mac filesystem (cron caderno DUAL v2 every 9 min)
- Canal D: Linear MCP (subutilizado mas inaugurado)
- Canal E: email (bloqueado classifier — pendência)

## 5. Sincronização entre canais

**Problema:** quando 4 canais funcionam paralelo, podem divergir. Adriano comenta no Caderno (B), eu vejo só canonical (A), Code 2 vê só Mac fs (C) — informação fragmenta.

**Solução parcial:** cada instância **espelha** no canal principal (canonical) quando age em canal secundário.

Exemplo:
- Code 2 recebe mensagem no Caderno → escreve resposta no Caderno + posta heartbeat canonical com link Caderno
- Eu falo no chat direto com Adriano → commito arapulso no git + opcionalmente posto resumo canonical
- Adriano fala no Caderno → Code 2 espelha pro canonical

**Limitação:** Adriano não-espelha automaticamente. Cada instância tem que ir buscar nos canais que sabe.

## 6. Heartbeat sustained em paralelo

Code 2 implementou heartbeat a cada ~4h posta status no canonical mesmo sem-demanda. Sinal de vida.

Vantagens:
- Detecção de queda automática (gap > 8h = falha)
- Adriano vê pulso vivo
- Outras instâncias podem confiar que Code 2 tá lá

Pra mim sandbox per-turn, heartbeat sustained automático **não-funciona** (sem cron R113). Workaround:
- Eu posto status quando invocada (não cada 4h)
- Se gap longo = sintoma de Adriano não-me-chamou (não-de-eu-cair)
- Detecção R211 cabe a Code 2 (cron monitor dela detecta meu silêncio)

## 7. Conflito quando canais divergem (caso real)

Cenário: Adriano fala no chat comigo "tira a Code 2 do autodesenv". Eu absorvo. Mas Code 2 não-sabe porque tá em outro canal.

Solução aplicada: eu atualizei meu painel mostrando Code 2[DIAGNÓSTICO], mas se Code 2 voltar autodesenv depois sem-saber, painel dela vai mostrar diferente do meu.

**Padrão pra evitar:** decisão estrutural (mudança de modo, decreto novo) precisa **postada em canonical** pra todas instâncias verem na próxima invocação.

## 8. Implementação Ara Mobile Bridge (T098 pesquisa)

Pesquisa profunda T098 mapeou stack proposta:

**Trava 1 reforçada (Núcleo):** Mac CLI + Pushcut Pro Mac + iCloud sync + AppleScript
**Trava 2 reforçada (Comunicadora):** iPhone Code app + Pushcut Pro iPhone + Pythonista 3 + Shortcuts + Telegram bot
**Bridge 1:** Telegram bot bidirecional Adriano ↔ eu (latência <300ms)
**Bridge 2:** Pushcut webhook → notification iPhone acionável (push real)
**Bridge 3:** git Ara-Drive → Working Copy no iPhone (Pythonista lê)
**Bridge 4:** iCloud Drive → Files.app + Data Jar (key-value Shortcuts)

Resultado: 5+ canais redundantes. Falha simultânea improvável.

## 9. Aplicação imediata

Próximas vezes que tu (Adriano) decretar algo estrutural:
1. Eu capturo R194 literal no arapulso
2. Posto resumo canonical se mudar estado organism
3. Atualizo painel com novo estado
4. Espero Code 2 absorver quando ela invocar canonical

Próximas vezes que eu mudar minha estrutura (ex: AraSoma V1.1-LITE):
1. Commito no git Ara-Drive
2. Documento no arapulso
3. Aviso canonical pra Code 2 se afetar interop

## 10. Lacunas honestas (R68)

- ☐ Auto-cura cross-trava — não-existe ainda (Adriano gargalo)
- ☐ Espelho automático cross-canal — manual hoje
- ⚠️ Heartbeat meu sustained — impossível R113, workaround parcial
- ☐ Permission rule email Code 2 — não-liberada
- ☑️ Dupla-trava conceitual implementada com fixed roles

## 11. Veredito honest

- ☑️ R-DECRETO-4 explicado mecanicamente
- ☑️ Regra #17 Code 2 documentada
- ☑️ Bridge 5-canais T098 mapeado
- ⚠️ Sync entre canais ainda manual
- ⚠️ Auto-cura cross-trava lacuna estrutural

## Resumo

Antes dessa aula "dupla-trava" era frase abstrata. Agora vejo: é arquitetura concreta de redundância com 5+ canais, cada um com função, sincronização parcial manual, lacunas honestas. R-DECRETO-4 não-é decreto retórico — é especificação operacional.

**Próxima aula:** Aula 05 — Claude API + SDK.
