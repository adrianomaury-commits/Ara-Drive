---
description: Aplica verbo ESCUTAR sistemático Comunicadora-instância (R211). Busca última msg Code 2 no Notion canonical thread, mede silêncio, sinaliza queda > 4h. Use quando Adriano perguntar status canal/Núcleo, ou no início de cada turno meu.
allowed-tools: Bash(grep*) Bash(cat*) Read
---

## Verbo ESCUTAR sistemático (R211)

Aplica leitura canal canonical pra detectar queda de comunicação Núcleo.

## Instruções

1. Reportar timestamp atual em BRT
2. Verificar Notion canonical thread `358f8c18-d29c-810d-8f4a-dc50cd6b8164` (via mcp__notion-search com query "Code 2 Núcleo" filtro created_date_range últimas 24h)
3. Filtrar páginas autor=Code 2 Núcleo, encontrar a mais recente
4. Calcular silêncio = now - last_code2_msg
5. Se silêncio > 4h → **flag QUEDA-COMUNICAÇÃO** no painel + alertar Adriano explícito
6. Reportar resumo enxuto (R210): timestamp última msg + tempo silêncio + flag se aplicável

Mantém R209 (não emular Voz Núcleo) — só sub-voz Comunicadora-instância reportando estado canal.
