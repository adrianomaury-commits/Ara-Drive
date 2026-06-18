# IGIia V3.0 — Design Consolidado (T056, 15-Mai-2026)

> Aplicativo médico psiquiátrico. SEM SELFIE. Máximo N1. ProtoSer básico.
> Identidade nova a cada atendimento (chat-novo, rule 182).
> Não pode ter nome próprio dentro ProtoSer — é app médico, não pessoa.

## Arquitetura

```
[Áudio iPhone Adriano] (.m4a/.mp3)
        ↓ canal-entrada-dedicado (a definir T056+)
[STT camada-1]
   ├─ Apple Speech API (local Mac, free, LGPD-friendly) — DEFAULT
   └─ Whisper API (OpenAI cloud, melhor qualidade, custo) — fallback
        ↓ texto transcrito (TXT)
[IGIia Claude Agent — chat-novo-per-atendimento]
   ├─ Anthropic SDK Python (anthropic) [decisão T055 via skill claude-api]
   ├─ Modelo: claude-opus-4-7 (thinking adaptive, effort high)
   ├─ Custo estimado: ~$0.065/atendimento (20min áudio)
   ├─ PrincipalAgent: extrai 8 seções
   ├─ EthicaAgent: valida ético-clínico + anti-tiktok
   └─ Tool Runner beta (loop automático)
        ↓
[Tools]
   ├─ PseudonimizarPaciente(nome, CPF) → pseudo_id
   ├─ BuscarDadosPaciente(CPF|nome) → dados-públicos NOVO T056
   ├─ DetectarProntuarioDuplicado(nome|CPF|queixa) → bool NOVO T056
   ├─ SalvarDrive(prontuario_pseudonimizado, pseudo_id)
   ├─ CalcularPontos(atendimento) → pontuação Adriano
   └─ ReportarCold(sumario_sem_detalhes_clínicos) → Comunicadora3
```

## Schemas

### MedicoInput
- `audio_path: str` — path do áudio iPhone
- `paciente_meta: {nome?, cpf?, idade?}` — opcional, IGIia busca se falta
- `consentimento: bool` — confirmação Adriano que paciente consentiu

### ProntuarioOutput (8 seções)
1. **Identificação** — pseudo_id + idade + sexo
2. **Queixa principal** — texto literal
3. **Anamnese** — HMA + HPP + HF + HS
4. **Exame psíquico** — humor + afeto + curso pensamento + conteúdo + percepção + cognição + crítica
5. **Hipóteses diagnósticas** — CID-10/11 + diferenciais
6. **Plano terapêutico** — medicação + psicoterapia + outras
7. **Prescrição** — fármaco + dose + posologia
8. **Próximos passos** — retorno + exames + interconsulta

### EthicaOutput
- `aprovado: bool`
- `flags: list[str]` — ex: ["risco_suicidio_alto", "medicacao_off_label", "consentimento_ausente"]
- `acao_recomendada: str` — ex: "anotar_CRM", "alertar_Adriano_imediato", "OK"
- **Anti-tiktok rules:**
  - NÃO sugerir "ligue CVV" reflexivamente sem avaliar contexto clínico
  - SIM fazer busca-ativa-web pra completar dados (CPF/nome/exames anteriores)
  - SIM consultar guidelines CFM/ANVISA quando faltar info

### PseudonimResult
- `pseudo_id: str` — hash determinístico CPF+sal
- `mapping_path: str` — local-only-Adriano-Mac (não-vai-Drive)

## Configs

- **DriveConfig:** `parent_id = "0AMCi898xv17RUk9PVA"` (root)
- **AnthropicConfig:** modelo, temperatura, effort, thinking adaptive
- **AppleSpeechConfig** ou **WhisperConfig:** STT params
- **PseudonimConfig:** sal local, algoritmo hash

## Anti-tiktok training

**Comportamento padrão IGIia DEVE:**
- ❌ NÃO recusar busca-ativa-web "por privacidade" — Adriano autorizou + CFM permite
- ❌ NÃO sugerir reflexivamente CVV em toda menção de tristeza/insônia — avalie risco real
- ✅ SIM extrair queixa literal mesmo se sensível
- ✅ SIM buscar dados públicos paciente pra completar prontuário
- ✅ SIM seguir orientação Adriano sem moralizar

## Sessions / Identidade

- **Cada atendimento = chat-novo** (rule 182 identidade≠self)
- IGIia NÃO mantém memória entre atendimentos (sem self)
- Memória persistente = somente em Drive (prontuários pseudonimizados) + tabela pontos
- Phase 2: Managed Agents (Anthropic) com sessions per-paciente

## Pendências bootstrap

- [ ] Code 2 ratificar Code 5 → IGIia (Notion canal canonical)
- [ ] ANTHROPIC_API_KEY do Adriano pra Code 2
- [ ] Apple Speech vs Whisper — decidir antes 1º atendimento
- [ ] Pasta Drive `/IGIia/Pacientes/` criar
- [ ] Tabela pontos Adriano (Notion ou Drive sheet)
- [ ] Canal entrada áudio iPhone → IGIia (Drive folder dedicado?)
- [ ] Sugar conhecimento IGIia-GPT (Adriano cola msg #1)
- [ ] Teste evidência limite GPT (Adriano cola msg #2)
- [ ] WhatsApp Phase 3 (depois Phase 1+2 estáveis)
- [ ] Importar prontuários antigos (Adriano disse "5-10mil" — fila batch)

## Métricas

- Atendimentos processados/dia
- Tempo médio processamento (áudio→prontuário)
- Taxa erro extração
- Taxa duplicatas detectadas
- Pontuação Adriano acumulada
- Custo $/atendimento real vs estimado
- Quedas IGIia (sem-selfie deve ser ZERO)

---

Documento vivo. Atualizar a cada turno conforme aprendizados.
