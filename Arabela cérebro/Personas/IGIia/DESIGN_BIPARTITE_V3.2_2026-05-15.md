# IGIia V3.2 — Design BIPARTITE com Assinatura Digital

> Sacada Adriano T058: "Tem uma IA que vai ajudar a coletar os dados, a outra que vai
> receber os dados, só com a assinatura digital do médico. Que aí tem mais valor científico,
> não é só um monte de dados."

## Princípio

Separar **COLETA** (pode-errar, exploratória, sem-selfie, máximo-N1) de **REPOSITÓRIO**
(só-recebe-dados-validados-cientificamente-via-assinatura-Adriano). Gate humano-médico
no meio = peer-review aplicado.

## Arquitetura

```
[ÁUDIO/TEXTO/INPUT atendimento]
          ↓
┌─────────────────────────────────────────┐
│         IGIia-COLETORA                  │
│                                         │
│ Identidade: sem-self, N1, chat-novo    │
│ per-atendimento (rule 182)              │
│ Substrato: Code 5 (ex-Secreto, reset)   │
│ Modelo: claude-opus-4-7 + thinking adapt│
│                                         │
│ ┌─────────────────────────────────────┐ │
│ │ 1. STT (Apple Speech ou Whisper)    │ │
│ │ 2. PrincipalAgent extrai 8 seções   │ │
│ │ 3. BuscarDadosPaciente() rule 187   │ │
│ │ 4. EthicaAgent valida ético-clínico │ │
│ │ 5. Anti-tiktok rules                │ │
│ │ 6. Genera DRAFT IPF3 v1.1           │ │
│ └─────────────────────────────────────┘ │
└─────────────────────────────────────────┘
          ↓
┌─────────────────────────────────────────┐
│         🖋️ GATE — ADRIANO REVISA        │
│                                         │
│ • Lê draft                              │
│ • Corrige se necessário                 │
│ • ASSINA DIGITAL                        │
│   (CRM 52861197 RQE 46911 + timestamp)  │
│ • Atribui pontuação (ouro/prata/bronze) │
└─────────────────────────────────────────┘
          ↓
┌─────────────────────────────────────────┐
│         IGIia-REPOSITÓRIO               │
│                                         │
│ Identidade: sem-self, N1, chat-novo    │
│ Substrato: Code 3 (onboarding) OU       │
│            instância nova dedicada      │
│ Modelo: claude-haiku-4-5 (mais barato)  │
│                                         │
│ ┌─────────────────────────────────────┐ │
│ │ Validar assinatura                  │ │
│ │ Verificar não-duplicado             │ │
│ │ PseudonimizarPaciente()             │ │
│ │ SalvarDrive() pseudonim             │ │
│ │ Indexar (ARA_INDEX.json estilo)    │ │
│ │ CalcularPontos() Adriano agregado   │ │
│ │ ReportarCold() sumário Comunicadora │ │
│ └─────────────────────────────────────┘ │
└─────────────────────────────────────────┘
          ↓
[DRIVE PSEUDONIMIZADO + ÍNDICE BUSCÁVEL]
          ↓
[CORPUS CIENTÍFICO ADRIANO — PUBLICÁVEL]
```

## Vantagens

1. **Erro contido em Coletora** — não-polui repositório
2. **Repositório só ouro-padrão** — base científica publicável
3. **Adriano = autoridade humana especialista** (peer-reviewer + editor)
4. **Custo otimizado** — Coletora opus, Repositório haiku
5. **Auditabilidade** — assinatura digital + timestamp = trilha
6. **Anti-cascata** — Coletora alucina? Adriano filtra. Repositório não-recebe lixo.

## Implementação técnica

### IGIia-Coletora
- **Substrato:** Code 5 (reset, sem-self prévio = ideal)
- **API:** Anthropic SDK Python
- **Modelo:** `claude-opus-4-7` (extração precisa exige quality)
- **Thinking:** adaptive
- **Effort:** high
- **Tools:** PrincipalAgent + EthicaAgent + BuscarDadosPaciente + GeradorDraft
- **Output:** JSON draft IPF3 + flags + lacunas

### Gate Assinatura
- Adriano abre draft num app/web
- Visualiza prontuário formatado
- Edita campos se necessário
- Clica "ASSINAR" → backend gera:
  - Hash SHA-256 do conteúdo
  - Timestamp ISO 8601
  - CRM + RQE
  - (opcional) certificado digital ICP-Brasil
- Marca prontuário como `signed_at`, `signed_by`

### IGIia-Repositório
- **Substrato:** Code 3 (onboarding) ou nova-instância
- **API:** Anthropic SDK Python
- **Modelo:** `claude-haiku-4-5` (validação + indexação = simples, barato)
- **Tools:** ValidarAssinatura + PseudonimizarPaciente + SalvarDrive + Indexar + CalcularPontos
- **Storage:** /Arabela cérebro/Personas/IGIia/Pacientes/ + Drive
- **Índice:** ARA_INDEX.json estilo (arch sistema-nervoso GPT-T057)

## Pontuação Adriano (gamificação)

| Tier | Pontos | Critério |
|------|--------|----------|
| Ouro | 100 | Prontuário completo (8 seções) + busca-dados-sucesso + assinatura |
| Prata | 60 | Prontuário ~80% + lacunas-aceitáveis + assinatura |
| Bronze | 30 | Prontuário básico + assinatura |
| Não-pontuado | 0 | Não-assinado pelo Adriano (Coletora errou demais) |

## Pendências bootstrap

- [ ] Code 2 implementa IGIia-Coletora
- [ ] Code 3 ou nova instância assume IGIia-Repositório
- [ ] Backend assinatura digital (web app ou CLI Mac)
- [ ] Adriano integra fluxo no seu workflow clínico
- [ ] Migrar PACIENTE_F47_001 e PACIENTE_F40_002 (processados-por-Comunicadora3) pra fluxo formal
- [ ] Definir ANTHROPIC_API_KEY Adriano
- [ ] Decidir hosting (Mac local vs cloud Managed Agents)

## Migração casos pré-bipartite

PACIENTE_F47_001 (Ingrid) e PACIENTE_F40_002 (Gabriela) foram processados por mim
(Comunicadora3) sem-bipartite-formal-ainda. Quando IGIia bipartite bootar:
- Adriano revisa retroativamente
- Assina digital pós-fato
- Marca como `pontuacao_retroativa: true`
