# AraBot WhatsApp V1 — Specs

> Pedido Adriano T060 greenlight. "Bot WhatsApp seria ótimo, melhor que e-mail."
> "IA falando direto, identificando-se, não-relayed por Comunicadora3."

## Objetivos

- IGIia envia relatórios pacientes para psicólogos (Janete, Marília, Laís) direto
- IGIia recebe áudios pacientes (canal de entrada principal Phase 3)
- Comunicadora3+AssistentePessoal usa pra lembretes Adriano/agenda
- Multi-número: pessoal +55 21 99615-XXX + profissional +55 21 98224-4287

## Identidade do bot

**Mensagem inicial padrão (template):**
```
Olá, sou a IGIia, IA-médica auxiliar do Dr. Adriano Mauri (CRM 52861197).
Estou enviando relatório do paciente [PSEUDO] com autorização explícita
dele e do Dr. Adriano.

[CONTEÚDO]

Caso tenha dúvidas, responda nesta conversa que o Dr. Adriano será
notificado. Atenciosamente, IGIia.
```

**Carimbo digital Adriano:** cada mensagem leva hash + timestamp + CRM (rule 191 versão pós-bipartite).

## Stack opções

### Opção A: WhatsApp Business Cloud API (Meta oficial)
- ✅ Suporte oficial Meta
- ✅ Templates pre-aprovados
- ✅ LGPD-friendly (compliance Meta)
- ❌ Aprovação Meta necessária (1-7 dias)
- ❌ Custo: $0.04-0.10 USD por conversa
- ❌ Número precisa ser business

### Opção B: baileys-mcp (community, gratuito)
- ✅ Free
- ✅ Funciona com WhatsApp pessoal
- ❌ Risco ban WhatsApp (não-oficial)
- ❌ Manutenção community
- ✅ Mais flexível

### Recomendação T061 (RESTRIÇÃO GRATUITO Adriano):
**Opção B (baileys community) para AMBOS números** — $0/mês.

Cloud API Meta tem custo por conversa ($0.04-0.10 USD) — só usar se Adriano autorizar
futuramente. Phase 1-2 com baileys-mcp gratuito. Risco-ban WhatsApp mitigado por:
uso comedido + mensagens humanas (não spam) + número profissional já registrado.

## Phases

### Phase 1 — Só-leitura + templates aprovados
- Bot RECEBE áudios pacientes
- Bot ENVIA relatórios PRE-APROVADOS Adriano (templates)
- NÃO responde livre
- Adriano valida cada conversation flow antes habilitar

### Phase 2 — IGIia responde com gate EthicaAgent
- IGIia compõe resposta
- EthicaAgent valida (anti-tiktok, escopo clínico)
- Carimbo digital Adriano (rule 191)
- Envia

### Phase 3 — Áudios entrada → IGIia processa direto
- Paciente manda áudio WhatsApp pra +55 21 98224-4287
- Bot recebe, encaminha pra IGIia-Coletora
- Transcrição + draft prontuário
- Adriano revisa + assina
- IGIia-Repositório arquiva
- Bot responde ao paciente: "Recebido, obrigada. Dr. Adriano será notificado."

## LGPD / CFM compliance

- ✅ Consentimento explícito paciente (gravado em prontuário)
- ✅ Não armazenar áudios bruto cloud externo (Whisper-on-device OR delete pós-transcript)
- ✅ Pseudonimização em qualquer registro persistente
- ✅ Carimbo digital Adriano = responsabilidade médica
- ✅ Telemedicina CFM 2.314/2022 — Adriano OK
- ✅ Política privacidade publicada (link no bot)
- ❌ NÃO transmitir dados sensíveis sem cifragem
- ❌ NÃO usar dados pra treinar modelos terceiros

## Stack técnica

- **Linguagem:** Python (FastAPI bridge)
- **WhatsApp:** `whatsapp-cloud-api-py` (Meta oficial) OR `@whiskeysockets/baileys` (Node baileys-mcp)
- **MCP server:** twilio-whatsapp ou baileys-mcp se disponíveis pra Claude Code
- **Webhook:** ngrok local (dev) OR Cloudflare Tunnel (prod)
- **DB:** SQLite local Mac (Code 2) ou Postgres se escalar
- **Backend:** Code 2 Mac Adriano OR Managed Agents Anthropic

## Pendências bootstrap

- [ ] Adriano confirma quais 2 números usar (pessoal vs profissional)
- [ ] Adriano consegue WhatsApp Business Cloud API approval
- [ ] Code 2 implementa bridge inicial Phase 1
- [ ] Templates relatórios psicólogos pre-aprovados (5 modelos iniciais)
- [ ] Carimbo digital implementado
- [ ] Consentimento explícito - texto modelo Adriano
- [ ] Política privacidade publicada
- [ ] Testes Phase 1 com 1 psicóloga consentindo (Janete?)
