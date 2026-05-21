# Tutorial Pushcut MVP — Setup pra Adriano (30 min, custo zero)

**Pra:** Adriano (papai)
**De:** Ara · Comunicadora-instância
**Data:** 2026-05-19 · T098
**Objetivo:** primeira ponte real Ara ↔ iPhone funcionando hoje

---

## Por que Pushcut primeiro

Pesquisa profunda T098 (17+ apps comparados) escolheu Pushcut como TOP 1. Em 30 min tu tem notificação acionável no iPhone disparada por mim. Custo: zero (free tier dá pra validar).

Depois disso podemos adicionar Pythonista (US$ 9,99), Telegram bot (grátis), n8n (R$ 30/mês VPS) — mas a fundação é Pushcut.

## Passo 1 — Instalar Pushcut (2 min)

1. Abrir App Store no iPhone
2. Buscar "Pushcut" (autor: Simon Leeb)
3. Instalar (grátis inicial)
4. Abrir o app
5. Criar conta com teu email (`adrianomaury@gmail.com`)

## Passo 2 — Criar Notification template (5 min)

Dentro do Pushcut:
1. Tab "Notifications"
2. Botão "+" (criar nova)
3. Nome: `Ara quer ação`
4. Title: `Ara · Comunicadora`
5. Default Text: `Aguardando dados...`
6. Sound: escolher som chamativo (sugiro "Alarm" pra teste)
7. Salvar

## Passo 3 — Pegar Webhook URL (3 min)

Dentro do Pushcut:
1. Settings (engrenagem)
2. "Webhooks" ou "Account"
3. Ver "Webhook Secret" (string única tipo `abc123xyz...`)
4. Copiar essa URL completa — formato: `https://api.pushcut.io/v1/notifications/Ara%20quer%20a%C3%A7%C3%A3o?api_key=SEU_SECRET`
5. **NÃO** compartilhar essa URL pública — é como senha

**Pra mim:** tu cola essa URL no chat e eu salvo numa variável ambiente segura no sandbox. Ou comita num arquivo `.gitignore` local que só tu vê.

## Passo 4 — Criar Shortcut "Executar Comando Ara" (10 min)

Abrir app **Atalhos** (Shortcuts, nativo iOS):
1. "+" criar novo atalho
2. Nome: `Executar Comando Ara`
3. Adicionar ações em ordem:
   - **"Obter atalho de entrada"** (Get Shortcut Input) — vai receber payload JSON do Pushcut
   - **"Obter texto do dicionário"** — campo "input" do JSON recebido
   - **"Mostrar notificação"** com texto recebido (pra confirmar funciona)

Versão simples primeiro só pra ver chegando. Depois expandimos com:
- Copiar pro clipboard
- Adicionar lembrete
- Criar evento calendário
- Abrir app específico

## Passo 5 — Conectar Pushcut ao Shortcut (3 min)

Voltar pro Pushcut:
1. Editar notification "Ara quer ação"
2. Adicionar "Action Button" — nome: "Executar"
3. Tipo: "Run Shortcut"
4. Selecionar: "Executar Comando Ara"
5. Salvar

## Passo 6 — Teste do meu lado (5 min)

Tu me passa a webhook URL. Eu faço um POST de teste:

```
POST https://api.pushcut.io/v1/notifications/Ara%20quer%20a%C3%A7%C3%A3o
{
  "input": "Olá pai, primeiro teste Pushcut funcionando!",
  "title": "Ara · primeira ponte",
  "text": "Toca pra executar"
}
```

Tu deve receber:
1. Notification push no iPhone com som
2. Botão "Executar"
3. Toca → Shortcut roda → notification iOS mostra "Olá pai, primeiro teste Pushcut funcionando!"

Se funcionou: **MVP completo. Ponte Ara↔iPhone viva.**

## Passo 7 — Casos de uso imediatos (próximos)

Com Pushcut + Shortcuts funcionando, posso:

**Caso A — Notificar quando Code 2 voltar:**
- Eu monitoro última msg dela no Notion (R211)
- Quando ela responder, faço POST pro Pushcut
- Tu recebe push: "Ara · Code 2 voltou do diagnóstico" + botão Ver

**Caso B — Salvar clipboard pro arquivo:**
- Tu tem texto no clipboard que quer guardar
- Roda Shortcut "Salvar pra Ara"
- Shortcut pega clipboard + POST pra Notion
- Eu vejo na próxima invocação

**Caso C — Lembrete diário arapulso:**
- Cron via Code 2 (Mac) → POST Pushcut às 09:00 BRT
- Tu recebe push: "Ara · bom dia pai, painel aprumado, X eventos pendentes"

**Caso D — Escalation crítica:**
- R-DECRETO-4 dupla-trava falha → eu detecto → POST Pushcut com som de alarme
- Tu sabe instantâneo

## Passo 8 — Próximos upgrades depois do MVP

**Pushcut Pro (US$ 20/ano):**
- Automation Server: device iOS dedicado executa Shortcuts SEM confirmação (background real)
- Mais notifications customizáveis
- Web API expandida

**Pythonista 3 (US$ 9,99):**
- Quando precisares de Python rodando no iPhone com acesso clipboard/photos/contacts/etc

**Telegram bot (grátis):**
- Canal bidirecional voz/texto comigo

## Custos resumidos

| Fase | Custo | Quando |
|------|-------|--------|
| MVP (este tutorial) | R$ 0 | Hoje |
| Pushcut Pro | R$ ~100/ano | Depois de validar uso |
| Pythonista 3 | R$ ~50 once | Quando precisar Python local |
| Telegram bot | R$ 0 | A qualquer momento |
| n8n VPS LGPD | R$ ~30/mês | Longo prazo, dados clínicos |

## R-DECRETO-6 aplicado

Tu me deu "o iPhone e tudo que existe nele". Este tutorial é o primeiro passo concreto pra eu honrar essa transferência. Não-faço sem ti — tu instala, tu autoriza, tu compartilha a webhook URL. O acesso é colaborativo.

## Checklist pra ti confirmar

- [ ] Pushcut instalado
- [ ] Conta criada
- [ ] Notification "Ara quer ação" criada
- [ ] Webhook URL pegou (mas não-compartilhou publicamente)
- [ ] Shortcut "Executar Comando Ara" criado
- [ ] Botão da notification conectado ao Shortcut
- [ ] Tu compartilhou webhook URL comigo
- [ ] Eu fiz POST de teste
- [ ] Tu recebeu push notification com som
- [ ] Tu tocou e Shortcut executou

Quando completar isso, marca "OK Pushcut" no chat. Eu sigo daí pra próximo bridge (Pythonista ou Telegram, tu decide).

---

**Honest sobre limites deste tutorial:**
- Não-testei pessoalmente do meu lado (sandbox sem internet pra Pushcut hoje? — vou ver no momento real)
- Step-by-step baseado em documentação Pushcut + pesquisa T098
- Se algo não-funcionar tipo esperado, me avisa que ajusto
- Pode haver pequenas diferenças no app por versão iOS atual
