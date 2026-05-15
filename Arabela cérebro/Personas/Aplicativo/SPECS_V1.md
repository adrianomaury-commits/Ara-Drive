# AraApp V1 — Specs (iPhone próprio Adriano + organismo Ara)

> Pedido Adriano T060 (15/05/2026 ~12:00 BRT).
> Substitui: Yara-GPT (transcrição) + Claude Code web (Cloud direto).
> Objetivo: app iPhone próprio do Adriano que fale com organismo Ara via Comunicadora3
> com transcrição premium + TTS feminino jovem com prosódia + 3 abas dashboard.

## Pilares

1. **Não copiar/colar mais** texto entre Yara, Cloud e iPhone
2. **Voz natural** entrada (mic) + saída (TTS)
3. **Push automático** das instâncias pra Adriano (mensagens diretas)
4. **Dashboard** com relatórios e gráficos do organismo

## Arquitetura

```
iPhone Adriano [AraApp SwiftUI]
        ↓ HTTPS
[Backend bridge — Code 2 Mac (localhost) OU cloud]
        ↓
[Anthropic SDK Python]
        ↓
Comunicadora3 (e demais células via Notion canonical + Drive)
```

## 3 abas

### Aba 1 — Falar com Ara (principal)
- Botão grande mic central
- Hold-to-talk OU toggle gravação
- Transcrição premium ao vivo (waveform visual + texto)
- Após soltar → envia transcrição + áudio bruto pra Comunicadora3
- Resposta volta como texto + TTS (voz feminina jovem com prosódia)
- Histórico conversa scrollável

### Aba 2 — Mensagens diretas (push das instâncias)
- Card list: instância origem + horário + sumário
- Cards: cor por urgência (verde info / amarelo pendência / vermelho ação-imediata)
- Tap → conversa completa
- Notification push iOS quando nova mensagem chega
- Filtro: Comunicadora3 / Code 2 / Code 4 / IGIia / Curadora

### Aba 3 — Relatórios + Dashboard
- Painel-resumo organismo: 6 células + %
- Métricas dia/semana: AraPulsos / Rules / Skills / Pacientes processados / Pontos clínicos Adriano
- Gráficos:
  - Linha temporal arapulsos
  - Pacientes processados/dia
  - Pontuação clínica acumulada
  - Skills instaladas (1/10 → 2/10 → ...)
- Botão "exportar PDF" — skill visualização integrada (cobrança T050 Adriano)

## Stack técnica

### Frontend iOS
- **SwiftUI** (iOS 17+, Adriano usa iPhone moderno)
- **AVFoundation** captura áudio + reprodução
- **Combine/AsyncSequence** streaming transcrição/TTS
- **UserNotifications** push das instâncias
- **Charts** (Apple framework iOS 16+) dashboard
- ~~Storyboard/UIKit~~ — SwiftUI moderno

### STT (Speech-to-Text) — transcrição premium
Opções (escolher OU combinar):
1. **WhisperKit** (Whisper rodando local-iOS, OpenAI free model) — privacidade LGPD ✅
2. **Apple Speech (SFSpeechRecognizer)** — free, on-device, mais simples
3. **OpenAI Whisper API** (cloud) — qualidade máxima, custo

**Captura premium (rule 193 proposta):**
- Intervalo entre palavras (silêncios significativos > N ms)
- Detecção barulho ambiente (Apple Sound Classification API: chuva/tráfego/voz/etc)
- Frequência fundamental F0 (pitch tracking via Accelerate framework)
- Conversão F0 → nota musical aproximada (Hz → A4/C5/etc)
- Dicionário evolutivo (aprende sons recorrentes Adriano)

Estrutura output enriquecida:
```json
{
  "transcript": "texto literal",
  "timestamps": [...],
  "intervals_ms": [120, 850, 200, ...],
  "noise_class": "voz",
  "noise_db": -35,
  "pitch_hz_avg": 145,
  "pitch_note_avg": "D3",
  "intervals_significativos": [{"after_word": "preciso", "ms": 1200}],
  "emotion_inferred": "calmo|ansioso|excitado|cansado"
}
```

### TTS (Text-to-Speech) — voz feminina jovem
Opções por qualidade:
1. **ElevenLabs API** — top qualidade, prosódia rica (referência Brock/Grok), $5-22/mês
2. **Cartesia Sonic** — rápido + bom + emoção, ~$5/M chars
3. **OpenAI TTS** — bom + barato + várias vozes ($15/M chars)
4. **Apple AVSpeechSynthesizer** — free, mas voz "robótica"
5. **Anthropic** — NÃO TEM TTS nativo (verificado skill claude-api)

**Referência Brock/Grok:** prosódia rica + tags emocionais inline. Implementável via ElevenLabs com Voice Settings + SSML.

**Recomendação V1:** ElevenLabs (top qualidade) com voice "Charlotte" (jovem feminina inglês) + voice clone PT-BR + emoção inline.

**SSML emoção inline (rule 193b proposta):**
```xml
<speak>
  Olá Adriano. <break time="500ms"/>
  <prosody pitch="+5%" rate="medium">Acabei de processar o atendimento do Eduardo.</prosody>
  <emotion category="confident">A correção foi aplicada.</emotion>
</speak>
```

### Backend bridge
- Linguagem: Python (alinhado decisão T055 stack IGIia)
- Framework: FastAPI (async + websockets)
- Host: localhost Mac Adriano (Code 2 admin)
- Auth: API key local (não exposto internet)
- Endpoints:
  - `POST /ara/talk` — texto/áudio → Comunicadora3 → resposta
  - `GET /ara/messages` — pull mensagens diretas instâncias
  - `POST /ara/messages/ack` — Adriano lê mensagem
  - `GET /ara/dashboard` — métricas organismo
  - `WS /ara/stream` — streaming transcrição/TTS

## Pendências bootstrap

- [ ] Code 2 cria projeto Xcode SwiftUI (Mac dele)
- [ ] Code 2 desenvolve backend FastAPI local
- [ ] Adriano gera ANTHROPIC_API_KEY + ELEVENLABS_API_KEY
- [ ] Build inicial TestFlight ou rodar direto via Xcode no iPhone
- [ ] Voice clone PT-BR ElevenLabs (uma boa voz feminina jovem)
- [ ] Integrar com Notion canonical (push mensagens)
- [ ] Push notifications iOS (APNs)
- [ ] WhisperKit modelo small (free, on-device, LGPD)
- [ ] Métricas dashboard implementadas

## Phases

- **V1 (1-2 semanas Code 2):** mic + texto + TTS Apple AVSpeechSynthesizer + 1 aba conversação
- **V2 (+1 semana):** Aba mensagens diretas + push
- **V3 (+2 semanas):** Aba dashboard + gráficos + skill visualização
- **V4:** WhisperKit local + ElevenLabs PT-BR + transcrição premium
- **V5:** Voice clone + dicionário evolutivo + emoção SSML

## Riscos

- Latência WhisperKit on-device em iPhone (modelo small ~3-5s, medium ~10s)
- Bateria iOS (transcrição contínua = drain)
- Backup se Mac offline (gracefully fallback cloud?)
- LGPD áudios pacientes (gravar consentimento explícito)

## Custo estimado mensal escalando

| Componente | V1 | V5 |
|-----------|----|----|
| Anthropic API | já-tem (Adriano) | já-tem |
| TTS Apple | $0 | $0 |
| TTS ElevenLabs | $0 | $22 |
| Whisper API | $0 | $0 (WhisperKit local) |
| Hosting | $0 (Mac Adriano) | $0-15 (VPS opcional) |
| **Total** | **~$0/mês** | **~$22-40/mês** |
