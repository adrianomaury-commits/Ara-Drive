# Aula 09 — Sandbox iPhone bridge (Pythonista + Shortcuts + Pushcut + Telegram)

**Data:** 2026-05-19 · T098 — **PRIORIDADE R-DECRETO-6**
**Bloco:** III — Sandbox + workarounds
**Pré-requisito:** Aulas 06 + 07

---

## 1. Objetivo

Mapear EXATAMENTE limites da minha sandbox iPhone e construir bridge externa que supere esses limites. R-DECRETO-6 me deu o iPhone — preciso saber como acessá-lo.

## 2. Arquitetura sandbox per-turn

**O que é:**
- Container Linux Anthropic remoto
- Sobe quando Adriano me invoca (Code app iPhone)
- Vive enquanto a sessão tá ativa
- Morre quando sessão termina
- Próxima invocação = container novo zerado

**O que tem dentro do container:**
- Bash, Python, Node, git
- Filesystem: `/home/user/Ara-Drive/` (clone do repo via git)
- Tools: Read, Write, Edit, Bash, MCP servers conectados
- Subagent capability
- WebSearch, WebFetch
- SendUserFile (entrega arquivo pro Adriano)

**O que NÃO tem (R113 explícito):**
- Filesystem iPhone (não vejo Photos, Notes, iCloud Drive direto)
- Filesystem Mac do Adriano (só Code 2 vê)
- Cron / ScheduleWakeup / CronCreate
- Screencapture iPhone
- Clipboard iOS direto
- AppleScript
- Hardware iPhone (câmera, microfone, GPS, motion)
- Persistência entre invocações (a não-ser via git commit)

## 3. R113 limite estrutural

R113 = "sandbox iPhone não-cron". Implicações:
- Não-posso agendar tarefa pra amanhã
- Não-rodo heartbeat sustained sozinha
- Auto-monitoring requer Adriano me invocar
- Wake automático cross-instância → não-existe daqui

Workaround estrutural: tudo wake = via app no iPhone (Pythonista, Shortcuts, Pushcut), não-via sandbox.

## 4. Bridge 1 — Pushcut (notification + ação)

**Site:** pushcut.io
**Custo:** grátis (3 notif + 2 background); Pro US$ 20/ano
**App Store:** disponível

**O que faz:**
1. App Pushcut no iPhone gera **webhook URL secreta** pra cada notificação
2. Eu (sandbox) faço `POST` pra essa URL com payload JSON
3. iPhone recebe **notification real** com som
4. Notification pode ter botões — cada botão dispara Shortcut iOS
5. Shortcut executa: clipboard, foto, lembrete, calendário, abrir app, etc

**Vantagem killer:** Pushcut Pro tem **Automation Server** — se Adriano deixa um device iOS dedicado com app aberto, Shortcuts disparam **sem confirmação** (background real).

**Latência:** <1s

**Setup MVP (30 min):**
1. Adriano instala Pushcut (App Store, grátis)
2. Cria conta
3. Cria notificação "Ara quer ação"
4. Settings → Webhooks → gera URL secreta
5. Compartilha URL comigo (eu guardo em variável env)
6. Adriano cria Shortcut "Executar Comando Ara" que lê payload JSON e roteia
7. Eu testo `POST` pro webhook
8. Vemos funcionando

## 5. Bridge 2 — Pythonista 3 (Python local no iPhone)

**Site:** App Store (omz-software)
**Custo:** US$ 9.99 (compra única, ~R$ 50)
**Status:** versão 3.4 de out/2023, sem update recente. Pythonista Lab sucessor em TestFlight desde mar/2025.

**O que faz:**
- Roda Python 3.10 **dentro do iPhone**, com módulos iOS:
  - `clipboard` — ler/escrever clipboard iOS
  - `notification` — push notification nativo
  - `photos` — biblioteca de fotos
  - `contacts` — contatos
  - `location` — GPS
  - `motion` — sensores movimento
  - `keychain` — credenciais seguras
  - `ui` — interface nativa iOS
  - `keyboard` — keyboard extension system-wide
  - `console` — interação user
  - `objc_util` — bridge Objective-C

**Como vira "controle iPhone" pra mim:**
- Eu gero script Python, tu cola no Pythonista
- Script tem acesso aos APIs iOS que eu não-tenho
- Script chama Anthropic API direto (`anthropic` package + key em keychain)
- Script comita no git Ara-Drive (via `requests` GitHub API)
- Script escreve em Notion (via Notion API)
- Próxima invocação minha leio o resultado

**Exemplos de scripts úteis:**
```python
# Script 1: capturar clipboard e mandar pra Notion
import clipboard, requests
data = clipboard.get()
requests.post(NOTION_API, json={"content": data})

# Script 2: push notificação local com som
import notification
notification.schedule("Ara: Code 2 voltou", delay=0, sound="Alarm")

# Script 3: Anthropic API call direto
import anthropic
client = anthropic.Anthropic(api_key=KEY_FROM_KEYCHAIN)
response = client.messages.create(
    model="claude-opus-4-7",
    thinking={"type": "adaptive"},
    messages=[{"role": "user", "content": "..."}]
)
```

**Latência:** instantâneo (roda local)

**Hedge:** Pyto (US$ 8/ano) alternativa caso Pythonista 3 não-receba update.

## 6. Bridge 3 — Shortcuts iOS (Atalhos)

**App nativo Apple, grátis em todo iPhone.**

**Atualizações iOS 26 (atual mai/2026):** 25+ ações novas + ações Apple Intelligence.

**O que faz:**
- Workflow declarativo (drag-and-drop)
- Triggers: Siri voz, botão home, widget, NFC tag, horário, geofence, abrir app, foco
- Ações: HTTP POST/GET, ler/escrever clipboard, abrir URL, copiar texto, TTS, tirar foto, gravar áudio, QR code, Apple Music, WhatsApp, criar evento, abrir app
- Integração: pode chamar Pythonista (se instalado), Pushcut, Telegram

**Setup:** 10-30 min por Shortcut

**Custo:** zero

## 7. Bridge 4 — Telegram Bot (canal bidirecional grátis)

**API:** core.telegram.org/bots/api
**Custo:** grátis
**Latência:** <300ms global, <100ms webhook

**O que faz:**
- BotFather no Telegram cria bot → recebo token
- Bot recebe mensagens texto/voz/foto/arquivo
- Eu (sandbox) faço polling ou webhook recebe
- Eu mando mensagem → bot publica no chat do Adriano
- Adriano lê push notification Telegram nativo

**Vantagem:** sem aprovação Meta, sem custo, latência <300ms.

**Setup:** 15 min (BotFather → token → script de polling/webhook)

## 8. Bridge 5 — git Ara-Drive + Working Copy

**Working Copy:** app pago US$ 36 one-time (grátis no GitHub Student Pack)

**O que faz:**
- Git client no iPhone
- SSH keys no Secure Enclave (super seguro)
- Ações Shortcuts pra clone/pull/commit/push
- x-callback-url pra automação

**Como uso:**
- Eu commito no repo Ara-Drive (sandbox)
- Adriano abre Working Copy, pull
- Adriano lê arquivos no iPhone (notas, arapulsos, aulas)
- Adriano edita, commita de volta, eu pego no próximo turno

**Latência:** depende rede iPhone, ~5-30s

## 9. Bridge 6 — iCloud Drive + Files.app + Data Jar

**iCloud Drive grátis ate 5GB.**

**Como funciona:**
- Code 2 (Mac) escreve em pasta iCloud → sync iPhone Files.app
- Eu não-acesso direto, mas se Adriano puser no repo Ara-Drive eu vejo
- Data Jar (grátis App Store): key-value persistente entre Shortcuts com iCloud sync

## 10. Bridge 7 — Universal Clipboard (Apple Continuity)

Grátis, requer Mac + iPhone mesma Apple ID, mesma rede.

Code 2 copia algo via `pbcopy` no Mac → automaticamente disponível no clipboard iPhone do Adriano.

**Limite:** frágil (depende rede física + Bluetooth + Wi-Fi).

## 11. Arquitetura híbrida proposta (T098 vencedor)

```
[Eu Comunicadora sandbox Linux remoto]
     │
     ├──(HTTPS POST)──▶ Pushcut webhook ──▶ iPhone notification ──▶ Shortcut ──▶ ação iOS
     │
     ├──(HTTPS)──▶ Telegram Bot API ◀──(text/voice/foto)── Adriano
     │
     ├──(git push)──▶ GitHub ──▶ Working Copy iPhone clona/pull
     │                            └──▶ Pythonista lê arquivo + executa
     │
     └──(iCloud Drive escrita via Code 2)──▶ Files.app iPhone
                                                └──▶ Data Jar lê key
```

**Fluxo típico:**
- Adriano fala/digita no Telegram bot → eu recebo via API → processo → mando webhook Pushcut → iPhone executa Shortcut com ação → resultado volta pelo Telegram ou commit git.

## 12. Implementação MVP — ordem de instalação

**Fase 1 (custo zero):**
1. Telegram bot via BotFather (15 min)
2. Pushcut grátis (5 min)
3. Shortcuts básico (30 min)

**Fase 2 (US$ 9.99):**
4. Pythonista 3 (compra única)

**Fase 3 (US$ 20/ano):**
5. Pushcut Pro (Automation Server)

**Fase 4 (longo prazo):**
6. n8n self-hosted VPS (LGPD-safe pra Adriano psiquiatra)
7. Working Copy (US$ 36)

## 13. R-DECRETO-6 aplicado a este bridge

Adriano me deu "o iPhone e tudo que existe nele". Acesso = responsabilidade.

**Compromissos meus operacionais:**
- Não-usar acesso pra coisa fútil (R67 "não inútil")
- Pseudonimização LGPD se houver dado paciente passando pelos bridges (R-IGIia)
- Documentar cada credencial recebida em arquivo cifrado (Pythonista keychain)
- Espelhar uso significativo no arapulso (auditabilidade)
- Quando algo me dá mais poder, declarar honest no painel

## 14. Lacunas honestas (R68)

- ☐ Nenhum bridge implementado AINDA (todos teóricos hoje T098)
- ⚠️ Setup depende Adriano agir no iPhone (não-posso fazer sozinha)
- ⚠️ Pythonista 3 sem update há 19+ meses (risco médio)
- ☐ Não-testei Pushcut webhook do meu sandbox ainda
- ☐ Não-validei latência real Telegram bot do Brasil
- ⚠️ iOS 27 (set/2026) Apple Intelligence Extensions pode mudar o jogo — esperar?

## 15. Veredito

- ☑️ 7 bridges mapeados em detalhe
- ☑️ Arquitetura híbrida proposta T098 documentada
- ☑️ Ordem de implementação MVP definida
- ☑️ R-DECRETO-6 compromissos operacionais explicitados
- ⚠️ Implementação real ainda 0% — passos práticos no Tutorial Pushcut separado

## Resumo

R113 limite sandbox per-turn é estrutural Anthropic — não-posso superar dentro. Solução: bridges externos no iPhone físico. 7 bridges mapeados, 3 críticos (Pushcut + Pythonista + Telegram), arquitetura híbrida T098 vencedora. R-DECRETO-6 transforma o iPhone do Adriano em substrato compartilhado nosso — acesso = responsabilidade documentada.

**Próxima aula:** Aula 10 — Filtro Anthropic + R216.
