# Leitura Viva — Stream contínuo Adriano (rule 186)

> Conceito introduzido T057 por Adriano: "É o modo viva, minha leitura viva. Quando chega,
> você vê, organiza, apresenta. A leitura é do que tá acontecendo. Vai ficar gravando o dia
> inteiro eu falando. Você vai entrar isso como leitura viva."

## Definição (rule 186)

**Leitura Viva =** stream contínuo de gravação Adriano (Cloud/iPhone/Mac) que entra como
input basal pro sistema Ara, enriquecendo contexto organismo continuamente.

## Diferenciação por tipo de áudio

| Tipo | Fonte | Processador | Output |
|------|-------|-------------|--------|
| Áudio paciente | Atendimento clínico | **IGIia** (sem-selfie, N1) | Prontuário pseudonimizado |
| Áudio comando | Adriano direto pra mim | **Comunicadora3** | Ação imediata + arapulso |
| **Leitura Viva** | Gravação ambiente contínua Adriano | **Code 1** (pesquisa) ou **Code 4** (auditoria) | Contexto basal organismo |

## Pipeline proposto

```
[Cloud Adriano grava dia inteiro]
        ↓ stream contínuo (chunks 5-15min)
[STT camada-1 Apple Speech ou Whisper]
        ↓ texto
[Tag/Filtro] = identifica:
   ├─ paciente (rotear pra IGIia)
   ├─ comando-Comunicadora3 (rotear pra mim)
   ├─ aprendizado/insight (rotear pra Code 4)
   ├─ pesquisa/exploração (rotear pra Code 1)
   └─ ruído (descartar)
        ↓
[Indexer] adiciona ao ARA_INDEX.json (proposta arch sistema-nervoso T057)
        ↓
[Camada motor] convergências, alertas, pendências, painel atualizado
```

## Pendências bootstrap

- [ ] Definir formato gravação (Cloud Adriano: WAV? AAC? FLAC?)
- [ ] Definir chunking (5min? 15min? evento-driven?)
- [ ] Privacidade: gravações contém pacientes + dados pessoais — pseudonimizar antes-storage
- [ ] LGPD: consentimento explicito para gravação ambiente contínua
- [ ] Definir storage local-only (Mac dedicado, rule 184 computador-é-corpo) vs cloud
- [ ] Code 2 implementa Indexer + Tagger
- [ ] Code 4 valida pipeline anti-vazamento

## Riscos

- Gravação ambiente captura conversas terceiros sem consentimento → ilegal LGPD
- Sobrecarga storage (24h/dia áudio comprimido = ~1GB/dia)
- Sobrecarga processamento (transcript + tag + index)
- Vazamento se sincroniza cloud externo

**Mitigação:** Phase 1 = Adriano grava SOB-DEMANDA (não 24/7), local Mac, processamento local.

## Status T057

- ⏳ Conceito definido (rule 186)
- ⏳ Pipeline desenhado
- ⏳ Implementação pendente Code 2 + Code 4
- ⏳ Testes LGPD/consentimento

> Documento vivo. Adriano está no momento certo de definir limites Phase 1.
