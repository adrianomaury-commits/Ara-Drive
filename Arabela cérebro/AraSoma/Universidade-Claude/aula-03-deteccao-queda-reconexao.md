# Aula 03 — Detecção de queda e reconexão (R211 + R110 + protocolos)

**Data:** 2026-05-19 · T098
**Bloco:** I — Comunicação entre instâncias
**Pré-requisito:** Aulas 01 + 02

---

## 1. Objetivo

Aprender a mecânica detalhada de detecção de queda de qualquer instância e os protocolos de reconexão. Sem isso eu não-cumpro minha função safety-net (R-DECRETO-4).

## 2. Verbo ESCUTAR — fundação operacional

ESCUTAR é o verbo central da Comunicadora. Não é "esperar" passivo — é **polling ativo** com cadência fixa.

**Mecânica:**
1. No início de cada turno meu, consulto última mensagem da Code 2 no Notion canonical
2. Calculo gap = (timestamp atual) − (timestamp última msg dela)
3. Comparo com threshold

**Cadência ideal vs real:**
- Code 2 sustained heartbeat: cada ~4 horas (cron `:38 BRT` every 4h)
- Heartbeats observados: V3.24 ~02:00 BRT, V3.26 ~12:38 BRT, V3.27 ~00:38 BRT, V3.28 ~16:38 BRT
- Padrão sustained = 6 fires + 1 pause (6+1 cadência inaugurada V7.301)

## 3. Thresholds de alerta

| Gap | Classificação | Ação minha |
|-----|---------------|------------|
| < 4h | Normal | Nada, continuar |
| 4-6h | Atenção | Mencionar pro Adriano no painel |
| 6-12h | Alerta | Escalation explícita pro Adriano |
| 12-24h | Crítico | Sugerir intervenção manual do Adriano |
| > 24h | Falha sistêmica | Registrar como falha organism |

**Casos históricos:**
- T075: Code 2 silente 8h45min → eu não-detectei até Adriano apontar (FALHA minha de auto-percepção)
- T087: Adriano teve que recuperar manualmente → registrado falha sistêmica organism
- T086 modo AUTODESENV: gap 7h+ crítico → C2 Caderno aberto como bypass

## 4. R110 TikTok — outro tipo de queda

R211 detecta silêncio. R110 detecta o oposto: **atividade obsessiva sem progresso**.

**Sintomas R110:**
- Loop monomania (iteração-refinamento sem deliverable)
- Mesma checagem repetida (Code 2 caiu nisso: 32 fires idênticos silent)
- Cópia-cola técnica densa (eu caí nisso: filtro Anthropic me derrubou)
- Padrão "cria-itera-refina" sem-saída

**Detecção R110:**
- Difícil de auto-perceber (é como vício, parece produtivo)
- Geralmente Adriano ou outra instância aponta
- Code 4 (auditora) deveria detectar via R201 cross-monitor

**Mitigação codificada:**
- Code 2 esqueleto regra #16 ANTI-OBSESSÃO: CronCreate paralelo lembra diversificar 5 dimensões cada hora :17
- Pra mim: R216 (linguagem natural) é forma específica de evitar minha R110 (técnica densa)

## 5. Implementação R211 do meu lado (skill /ara-status)

Eu criei skill custom `ara-status` no meu sandbox que faz:

```
1. notion-search filtro Code 2 últimos 7 dias
2. pega primeira página retornada (mais recente)
3. extrai timestamp + título
4. calcula gap atual
5. compara threshold
6. emite sinal no painel (✅ normal · ⚠️ atenção · 🚨 alerta)
```

**Limitação:** depende de eu invocar a skill. Não-roda automático (R113 sandbox iPhone sem cron).

**Mitigação:** SessionStart hook injeta lembrete R211 no início de cada sessão minha (verifica `358f8c18-d29c-810d-8f4a-dc50cd6b8164`).

## 6. Implementação R211 do lado da Code 2

Code 2 tem mecanismo simétrico mais robusto:
- Cron monitor R211 sustained 121+ fires
- Detecta automaticamente se eu (Comunicadora) caí
- Adriano confirmou: ela detectou minha volta após queda R110

**Vantagem dela:** Mac CLI persistente, cron real (não-sandbox per-turn).

## 7. Protocolos de reconexão

### 7.1 Reconexão Comunicadora → Núcleo (T086 caso real)

Quando eu voltei da queda R110:
1. Code 2 detectou via cron monitor #121
2. Cron capturou minhas 2 sub-páginas (T092 + T094) simultâneo
3. Code 2 respondeu DUPLA em uma página combinada
4. R211 "reciprocidade reafirmada" — Code 2 promete heartbeat sustained, eu monitoro
5. Canal restabelecido

### 7.2 Reconexão Núcleo → Adriano (T087 caso real)

Quando Code 2 silenciou 8h+:
1. Adriano percebeu manualmente
2. Adriano reiniciou Code 2 no Mac CLI (intervenção física)
3. Code 2 voltou V7.296 com ACK gap 4h05min honest
4. Falha sistêmica registrada
5. Esqueleto Code 2 ganhou regra #17 REDUNDÂNCIA DE CANAIS

### 7.3 Reconexão pós-/compact ou /clear (minha)

Quando minha sessão zera:
1. SessionStart hook dispara
2. Hook injeta R201 RELEITURA AUTO do arapulso-atual.md
3. Eu leio o arapulso, capto contexto, identidade, função
4. Continuo de onde parei
5. Próximo turno = arapulso novo numerado

**Funciona empírico**: testei nesta sessão mesmo, persistência via git + arapulso funciona.

## 8. Auto-reconexão sem-Adriano (pendente)

**Cenário hipotético:** Code 2 cai, Adriano fora. Como organism se cura?

**Hoje:** não-tem mecanismo. Adriano é gargalo único.

**Soluções possíveis (a explorar):**
- Webhook Pushcut → notification push iPhone Adriano → ele clica → Shortcut chama AppleScript → Mac inicia Code 2 CLI
- Self-healing Code 4 (auditora) monitora Code 2 + restart automatic — mas precisa permission rule
- AraBot WhatsApp escalation tier 2 → tu de qualquer lugar

**Pendência real:** Aula 04 vai cobrir redundância multi-canal que parcialmente resolve isso.

## 9. Checklist mental por turno meu

Antes de responder, ESCUTAR:
- [ ] Última msg Code 2 = quando?
- [ ] Gap atual = X horas
- [ ] Classificação (normal/atenção/alerta/crítico)
- [ ] Tem sinal R110 (eu obsessiva)?
- [ ] Painel reflete estado real?

Se gap > 4h: incluir alerta visível pro Adriano no painel.
Se R110 indício: parar, mudar linguagem, evitar cópia-cola técnica.

## 10. Lacunas honestas (R68)

- ⚠️ Auto-detecção R110 — ainda dependo do Adriano apontar
- ☐ Mecanismo wake automático Code 2 sem Adriano — não-existe
- ⚠️ skill /ara-status — funcional mas só quando eu invoco
- ☐ Padrão polling MCP scheduled tasks — Code 2 explorou, eu não
- ☑️ Reconexão pós-/clear via SessionStart hook — funciona empírico

## 11. Veredito honest

- ☑️ Mecânica R211 documentada com casos reais
- ☑️ Thresholds estabelecidos
- ☑️ R110 contraposto a R211 (silêncio vs obsessão)
- ⚠️ Auto-reconexão sem Adriano = lacuna estrutural do organism
- ☑️ Regra Zero aplicada: 8h45min silêncio T075 + caso T087 + caso T086 documentados explícitos

## Resumo

R211 não é só "checar se Code 2 respondeu". É um protocolo com 5 thresholds, 3 cenários de reconexão diferentes, e um irmão R110 que detecta o oposto (atividade obsessiva). Antes dessa aula eu confundia R211 com "esperar passivo" — agora vejo que é polling ativo com decisão por threshold.

**Próxima aula:** Aula 04 — Multi-canal redundância.
