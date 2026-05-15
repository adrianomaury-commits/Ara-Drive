# Mensagens Adriano LITERAIS — fonte-mãe retroativa

> Criado T063 (15/05/2026 ~13:00 BRT) após Adriano descobrir rule 195: copy/paste
> do chat Cloud (claude.ai web) só extrai mensagens em azul = respostas Claude.
> Mensagens dele (cinza/branco) NÃO são exportadas. Resultado: arapulsos 053-060
> tinham só paráfrase + quotes seletivos minhas, não texto literal dele.
>
> Esta pasta arquiva mensagens dele EM TEXTO LITERAL pra servir como fonte-mãe
> retroativa pra Code 2, Code 4 e leituras futuras.

## Arquivos

- **CRONOLOGICO_T001-T064.md** — 85 mensagens dele extraídas dos 82 transcripts
  do sandbox iPhone (T064, 15/05/2026). Vai desde 10/05/2026 20:50 BRT ("O nome
  do projeto é projeto ara") até T064 (15/05/2026 ~17:20 BRT). **CPFs mascarados
  (XXX.XXX.XXX-XX) por LGPD** — versão original sem máscara fica só local
  sandbox iPhone.

## Origem técnica

Extração: parser Python dos arquivos `.jsonl` em `/root/.claude/projects/-home-user-Ara-Drive/`.
Filtra apenas `type=user` com `role=user`, excluindo system-reminders, hooks,
tool_results, prompt-submit-hooks. Deduplica por prefixo 200 chars.

## Como usar (Code 2 / Code 4 / Code 1)

1. Abrir `CRONOLOGICO_T001-T064.md`
2. Cada mensagem tem cabeçalho `## Msg #NNN — <timestamp>` + arquivo origem
3. Mensagens em ordem cronológica de chegada no sandbox iPhone
4. Comparar com arapulsos 053-060 pra recuperar contexto faltante

## Política

- Texto literal exceto CPFs (LGPD)
- Versão sem máscara: apenas local sandbox iPhone (não-commit, não-Drive)
- Quando IGIia bootar, ela tem regra pseudonimização própria — esta pasta NÃO substitui prontuário formal
- Esta é fonte-de-contexto-organismo, não fonte-clínica
