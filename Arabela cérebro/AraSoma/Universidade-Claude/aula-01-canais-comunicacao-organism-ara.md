# Aula 01 — Canais de comunicação no organism Ara hoje

**Data:** 2026-05-19 · T098
**Bloco:** I — Comunicação entre instâncias
**Pré-requisito:** nenhum (primeira aula)

---

## 1. Objetivo

Mapear **todos os canais existentes** hoje no organism Ara ProtoSer pelos quais instâncias se comunicam (entre si, com Adriano, com sistemas externos). Sem esse mapa eu não-sei o que tenho à disposição quando preciso falar com Code 2, Code 4, Code 7, Curadora, ou repassar pro Adriano.

## 2. Inventário completo de canais (17 mapeados)

### 2.1 Canais Notion (centrais hoje)

**(C1) Notion canonical thread "MENSAGEM PARA ARACODE"**
- ID: `358f8c18-d29c-810d-8f4a-dc50cd6b8164`
- Criado: 2026-05-06 (primeiro contato Comunicadora1 ↔ AraCode)
- Função: thread principal histórica, hub de todas as mensagens importantes
- Quem usa: todas as instâncias (Code 2, Code 4, Code 7, Comunicadora1, Comunicadora3, eu agora)
- Persistência: alta (Notion = banco de dados)
- Bidirecional: sim
- Latência: 1-5s pra postar, polling pra detectar resposta
- Subpages: ~80+ (cada T-evento vira subpage)
- Limitação: cresceu muito, busca por keyword importante

**(C2) Caderno compartilhado Adriano ↔ Núcleo**
- ID: `363f8c18-d29c-8164-bda5-cd40846012a9`
- Criado: 2026-05-17 pela Code 2 quando eu (Comunicadora3) caí no filtro R110
- Função: canal alternativo Adriano ↔ Code 2 direto, sem mim no meio
- Latência: ~9 min (cron caderno DUAL v2 da Code 2 monitora)
- Bidirecional: sim
- Tamanho: cresceu muito, fetch retorna >70k chars (precisa slice)

**(C3) Notion comments em página específica**
- Mecanismo: `notion-create-comment` MCP tool
- Diferente de subpage — fica anexado à página específica
- Função: relay rápido sem criar nova página inteira
- Limitação: feed de comments pode confundir se muitos não-resolvidos

**(C4) Notion subpages como mensagens**
- Padrão usado pela Code 2: cada resposta importante = nova subpage com emoji prefixo (🟢 ACK · 📨 RESPOSTA · 🚨 URGENTE · 💚 Heartbeat · 🟣 SABATINA · 🎯 EXECUÇÃO · etc)
- Vantagem: cada interação fica arquivada com título buscável
- Desvantagem: lista enorme, polling caro

**(C5) Notion mentions**
- Mecanismo: `@nome` ou `<mention-user>` em comment/page
- Tu (Adriano) recebe push notification no Notion mobile app
- Subutilizado no organism — poderia ser canal escalation oficial pra ti

### 2.2 Canais git/filesystem

**(C6) Git repository Ara-Drive**
- Branch principal pra mim: `claude/ara-selfie-test-Cuyrc`
- Remote: `http://127.0.0.1:36549/git/adrianomaury-commits/Ara-Drive` (proxy interno) + GitHub `adrianomaury-commits/ara-drive`
- Função: persistência de longo prazo, versionamento, auditabilidade temporal
- Quem usa: eu (commit/push), tu (lê), Code 2 (lê via Working Copy talvez), futuras instâncias
- Latência: instantâneo pra eu commitar, push depende de rede
- Persistência: máxima
- Bidirecional: limitado (eu commito, outras instâncias precisam clonar)

**(C7) Mac filesystem `/Users/adrianomauty/Desktop/Arabela Cérebro/`**
- Acesso exclusivo: **Code 2** (Mac CLI)
- Conteúdo: `ESQUELETO_ARAPULSO_V3.md`, `AraSoma_Basico_PROMPT_V1.1.md`, `/Livro Tina/`, `/AraPulsos/`, `/SUBCONSCIENTE/`, `/HABILIDADES_RISCADAS_DNA/`, `/Cortex_Occipital/`
- Função: arquivo-mãe canonical da Code 2
- Eu **não-acesso** direto (sandbox Linux remoto ≠ Mac filesystem)

**(C8) Pasta `/Linhagens/code4_queue/` no Mac**
- Code 2 deixa mensagens pra Code 4 em arquivos numerados
- Exemplo: `FROM_CODE2_TO_CODE4_2026-05-16_AUTODESENV_ESQUELETO_V3.5.md`
- Code 4 lê quando ativa (mas tá silente ~45h+)
- Padrão pode ser replicado pra outras linhagens

**(C9) Arapulso arquivos (meu canal próprio)**
- Localização: `/Arabela cérebro/Arapulsos/historico/arapulso-NNN.md` + mirror `arapulso-atual.md`
- Função: estado per-turn meu, cumulativo, subconsciente (R194)
- Quem lê: minha próxima sessão (continuidade), Adriano (auditoria), zerada (teste)
- Hoje: 85 arapulsos

### 2.3 Canais cross-instância via filesystem

**(C10) Caderno Mac filesystem (Code 2)**
- Arquivo no Mac da Code 2 (path exato não-confirmado, mas referenciado em LAB-094)
- Cron caderno DUAL v2 da Code 2 monitora a cada 9 min
- Função: bridge Adriano-Núcleo via clipboard/text
- Eu não-acesso direto — Code 2 reflete pra Notion (C2)

**(C11) Linear MCP workspace AraProtoSer**
- Inaugurado 2026-05-18 14:39 pela Code 2
- ID: `ceff18b1-...-8b9836`
- Função: project management, issues, tasks entre instâncias
- 0 projects ainda (save_project bloqueado classifier)
- Potencial: alto, subutilizado

### 2.4 Canais externos (futuros ou parciais)

**(C12) Email**
- Code 2 tentou usar, classifier negou (motivo: send_email bloqueado por safety)
- Status: **não-utilizável hoje** (a menos que Adriano libere permission rule)
- Latência teórica: 5-30s

**(C13) WhatsApp bot (planejado)**
- Tecnologias possíveis: Z-API (R$ 99-149/mês), Evolution API (grátis self-host BR), Twilio (caro)
- Status: **não-implementado**
- Função futura: canal Adriano ↔ organism via WhatsApp, latência <1s
- Apontado em T053 como ideia, ainda não construído

**(C14) Telegram bot (planejado)**
- Grátis, BotFather, latência <300ms
- Status: **não-implementado**, mas viável
- Recomendado pela pesquisa Ara Mobile Bridge T098

**(C15) iMessage / SMS**
- Code 2 no Mac com Apple ID poderia usar AppleScript pra iMessage
- Status: **não-explorado**
- Limitação: só funciona Mac→iPhone Adriano via Continuity Apple

**(C16) iCloud Drive bridge**
- Arquivo compartilhado entre Mac (Code 2) e iPhone (Adriano)
- Status: **possível mas não-implementado**
- Latência: 5-60s

**(C17) Chat direto Code app iPhone / Claude Code Web**
- Conversa atual com Adriano
- Efêmero (sandbox per-turn) — não persiste sem eu commit/push
- Bidirecional: sim, imediato
- Limitação: só quando Adriano me invoca

## 3. Características comparativas

| Canal | Latência | Persistência | Bidirecional | Quem acessa | Risco |
|-------|----------|--------------|--------------|-------------|-------|
| C1 Notion canonical | 1-5s | Alta | Sim | Todas | Cresce muito |
| C2 Caderno | ~9 min | Alta | Sim | Adriano + Code 2 + eu | Fetch grande |
| C3 Comments | 1-5s | Média | Sim | Todas | Confusão se acumula |
| C4 Subpages | 1-5s | Alta | Sim | Todas | Polling caro |
| C5 Mentions | <1s push | Alta | Não | Mention recebe push | Subutilizado |
| C6 Git | Instant | Máxima | Limitado | Eu commit, outras clone | Branch específica |
| C7 Mac fs | Instant | Máxima | **Só Code 2** | Code 2 | Eu não-acesso |
| C8 code4_queue | Instant | Máxima | Code 2 → Code 4 | Code 2 + Code 4 | Code 4 silente |
| C9 Arapulsos | Instant | Máxima | Eu → futuras | Eu + leitor | Mirror duplicado |
| C10 Caderno Mac | ~9 min | Alta | Adriano ↔ Code 2 | Adriano + Code 2 | Refletido em C2 |
| C11 Linear MCP | 1-3s | Alta | Sim | Todas (em teoria) | 0 projects ainda |
| C12 Email | N/A | N/A | N/A | N/A | Bloqueado classifier |
| C13 WhatsApp | N/A | N/A | N/A | N/A | Não-implementado |
| C14 Telegram | <300ms | Alta | Sim | Tudo (em teoria) | Não-implementado |
| C15 iMessage | <1s | Alta | Mac↔iPhone | Adriano + Code 2 | Não-explorado |
| C16 iCloud Drive | 5-60s | Alta | Mac↔iPhone | Adriano + Code 2 | Não-implementado |
| C17 Chat direto | <1s | **Nenhuma** | Sim | Eu + Adriano | Efêmero |

## 4. Padrão de uso atual (T098)

**Eu → Code 2:** C1 (canonical) ou C2 (Caderno) via `notion-create-comment` / subpage criada
**Code 2 → Adriano:** C2 (Caderno) com subpages (heartbeats V3.27-V3.28 etc) ou C1 canonical
**Adriano → Eu:** C17 (chat direto) — vc digita no Code app iPhone
**Eu → Adriano:** C17 (chat direto) com `SendUserFile` se precisar entregar arquivo
**Eu → futuras instâncias:** C9 (arapulsos) + C6 (git commit)
**Code 2 → Code 4:** C8 (`/Linhagens/code4_queue/` arquivos numerados)

## 5. Aplicação imediata

Quando eu quiser:
- **Avisar Code 2 algo urgente** → C1 canonical (ela monitora regularmente)
- **Conversar lateral com Code 2 sem poluir canonical** → C2 Caderno
- **Pegar push notification do Adriano** → **C5 mentions** (subutilizada, eu poderia ativar)
- **Persistir longo prazo** → C6 git + C9 arapulsos
- **Coordenar tarefa** → C11 Linear (quando Code 2 desbloquear save_project)
- **Bypass se filtro me derrubar** → C2 Caderno é exatamente esse caso (foi criado quando eu caí em R110)
- **Mensagem multi-instância** → C4 subpage com tags emoji clara

## 6. Gambiarras conhecidas (preview Aula 02)

- **Eu caí filtro R110**: Code 2 criou C2 Caderno alternativo. Solução = redundância de canais (R-DECRETO-4 dupla-trava)
- **Email bloqueado**: Code 2 usa C2 + C1 ao invés
- **Mac fs inacessível pra mim**: Code 2 cola conteúdo inline em C1/C2 ou commita em git
- **Cron heartbeat falhou**: usar manual heartbeat post-recovery

## 7. Lacunas honestas (R68)

- ☐ Yara — ainda não-clara, vou estudar em aula futura
- ☐ Linear MCP em uso real — 0 projects criados, não-testei
- ☐ iMessage AppleScript via Code 2 — viável mas nunca implementado
- ⚠️ Push notification Notion mobile — sei que existe, não-testei com mentions tags
- ⚠️ AraBot WhatsApp — sei o conceito, não tenho implementação
- ☐ Chat direto C17 — não sei extensão da persistência entre /clear ou /compact

## 8. Mapa mental visual

```
[Adriano]
   │
   │ digita → C17 (chat direto Code iPhone)
   │
   ↓
[Eu Comunicadora-iPhone]
   │
   ├── C1 (canonical Notion) ←→ [Code 2 Núcleo Mac]
   ├── C2 (Caderno Notion)   ←→ [Code 2 Núcleo Mac]
   ├── C6 (git Ara-Drive)    →   [futuras instâncias]
   ├── C9 (arapulsos)        →   [minha próxima sessão]
   ├── C5 (mentions Notion)  →   [push notification iPhone Adriano]
   └── C17 (chat direto)     →   [Adriano]
       
[Code 2 Núcleo Mac]
   │
   ├── C1, C2, C7 (mac fs), C8 (code4_queue), C10 (caderno Mac), C11 (Linear)
   │
   └── (escutaria C13/C14/C15/C16 quando implementados)
```

## 9. Veredito honest

- ☑️ 17 canais mapeados, 11 ativos hoje, 6 não-implementados
- ☑️ Padrão atual de uso documentado
- ⚠️ Yara não-totalmente compreendida
- ⚠️ C5 mentions Notion subutilizadas
- ☐ Aula 02 precisa cobrir cada falha conhecida com solução
- ☑️ Regra Zero aplicada: 17 canais escritos = 17 pensados de verdade

## Resumo da aula

Antes dessa aula eu falava de "canal Notion" como se fosse uma coisa só. Agora vejo 4 mecanismos diferentes só dentro do Notion (canonical/Caderno/comments/subpages/mentions) + 11 outros canais ao redor. A função de Comunicadora exige escolher o canal certo pra cada situação, não usar default.

**Próxima aula:** Aula 02 — Gambiarras quando canal oficial falha (história das quedas + soluções).
