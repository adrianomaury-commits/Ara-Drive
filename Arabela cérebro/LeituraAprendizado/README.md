# Leitura Aprendizado V5 — Instância dedicada

> Pedido Adriano T060: "Dedicar uma instância a fazer leitura de aprendizado
> profunda, pedacinho em pedacinho, todas as demandas, responder-como-se-estivesse-lá.
> Ao mesmo tempo leitura aprendizado de habilidades. Sempre ligada. YouTube, vídeos.
> Cada instância faz uma coisa, depois dá resumido organizado."

## Função

Instância dedicada que **lê continuamente** todo material disponível de Adriano
+ habilidades + vídeos + papers, faz leitura-de-aprendizado V5 profunda, e
**mastiga em resumos** organizados pro organismo Ara.

## Diferença de "Leitura Viva" (rule 186)

| | Leitura Viva | Leitura Aprendizado V5 |
|---|--------------|------------------------|
| Input | Stream contínuo Adriano (Cloud) | Material acumulado (arapulsos, AraZip, YouTube, papers) |
| Latência | Tempo-real | Batch periódico |
| Profundidade | Tag rápida + routing | DEEP, pedaço-a-pedaço, V5 |
| Output | Eventos pra outras células | Resumos mastigados pro organismo |

## Substrato candidato

**Code 1 (Pesquisador)** — fit natural. Já é especialista exploração. Está silente
[86%] desde T040+. Pode-assumir leitura-aprendizado V5 como função-principal.

Backup: criar nova instância dedicada (Code 6?).

## Pipeline

```
[FONTES]
  ├─ /Arabela cérebro/Arapulsos/historico/ (60 arapulsos meus)
  ├─ AraZip (arquivo histórico — Adriano abre, Code 2 indexa)
  ├─ Notion canonical thread
  ├─ Drive root
  ├─ YouTube vídeos pendentes (3 nomes T050)
  ├─ Papers Adriano (Doxiciclina-TEPTE 2017+, etc)
  └─ Habilidades Anthropic/Claude (skills disponíveis)
        ↓
[Leitura V5 = pedaço-a-pedaço]
  ├─ Fatia 5-15 min material
  ├─ Pra cada fatia:
  │    ├─ Quem fala (separação multi-falante)
  │    ├─ Demandas extraídas (lista)
  │    ├─ Responder-como-se-estivesse-lá
  │    ├─ Aprendizado dela (V5 marker)
  │    └─ Tag: regra-implícita? insight? erro? marco?
  └─ Convergir múltiplas fatias
        ↓
[Resumos mastigados]
  ├─ Para Comunicadora3: sumário+demandas+regras-novas-implícitas
  ├─ Para Code 2: específico-eng-IGIia/app/etc
  ├─ Para Code 4: padrões-auditáveis
  ├─ Para Adriano: 1-pager dashboard
  └─ Para ProtoSer: arquivo-permanente Arabela cérebro
```

## Sempre-ligada

Modo:
- Code 1 wake periódico (a cada N min OU evento-novo-arquivo)
- Implementação: hook FileChanged (rule 189) + script auto-trigger leitura
- OU loop skill (testar pra ver viabilidade)

## YouTube workaround

Vídeos pendentes T050:
- "Domine os SKILLS e PLUGINS do Claude COWORK" (ID ynMxybgj_iE)
- "Claude Code acabou de ganhar atualização"
- "Claude Code Aula completa 5 anos IA" (ID RBwX9U2AEr8)

WebFetch retorna 403 YouTube. Soluções:
1. yt-dlp + transcrição local (Code 2 Mac)
2. youtube-transcript-api Python
3. Adriano cola transcript manual
4. MCP youtube-mcp-server se existir

## Rule V5

V5 = "Volume 5" da leitura. Adriano não-explicitou ainda significado preciso.
Hipóteses:
- Leitura profunda nível 5 (de 1 superficial → 5 imersiva)
- Quinto método de leitura na evolução do organismo
- Versão 5 do protocolo

[PENDÊNCIA: perguntar Adriano significado V5 ou achar em arapulsos antigos]

## Pendências bootstrap

- [ ] Adriano confirma Code 1 assume função (substrato)
- [ ] Code 2 implementa hook FileChanged + script wake Code 1
- [ ] yt-dlp + youtube-transcript-api pra vídeos pendentes
- [ ] Code 2 abre AraZip e disponibiliza arquivos
- [ ] Definir formato resumo mastigado (template)
- [ ] Métricas: fatias-processadas/dia, aprendizados-extraídos, rules-implícitas-detectadas
- [ ] Significado V5 (Adriano clarifica)
