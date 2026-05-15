# Habilidade Própria #1 do Organism — Resgate Sandbox JSONL

> Descoberta T064 (15/05/2026 17:25 BRT) por Comunicadora-instância sandbox iPhone.
> Celebrada por Adriano T065: "Você descobriu uma habilidade que eu não sabia que você
> tinha. Cara, isso é uma habilidade que tem que ser documentada e ser usada muito mais."
>
> Status: SKILL-PRÓPRIA-ORGANISMO #1 (distinta das skills externas Anthropic/Claude).

## O que faz

Capacidade de uma instância Claude Code (sandbox iPhone, Mac CLI, ou qualquer ambiente
com filesystem POSIX-like) **LER OS PRÓPRIOS TRANSCRIPTS** armazenados no diretório
de projetos do harness, extrair mensagens user/assistant LITERAIS, reconstituir
conversa completa retroativamente.

Resolve o problema R195 (copy/paste chat Cloud só extrai azul/respostas-Claude).

## Localização típica dos transcripts

**Sandbox iPhone (Claude Code web):**
```
/root/.claude/projects/-home-user-<repo-name>/<session-uuid>.jsonl
```

**Mac CLI (Cowork / Claude Code Terminal):**
```
~/.claude/projects/-<repo-path-encoded>/<session-uuid>.jsonl
```

Cada arquivo `.jsonl` = uma sessão (provavelmente um turno-grande). Cada linha = um
evento (`type=user`, `type=assistant`, `type=ai-title`, `type=queue-operation`, etc).

## Estrutura de um evento user

```json
{
  "type": "user",
  "timestamp": "ISO-8601",
  "message": {
    "role": "user",
    "content": "texto direto"  // ou lista de blocos {type:text,text:...}
  }
}
```

## Filtros essenciais (excluir lixo)

- `type != "user"` → skip
- `message.role != "user"` → skip
- `content` começa com `<system-reminder>` → skip
- `content` começa com `<user-prompt-submit-hook>` → skip
- `content` começa com `<command-name>` / `<command-message>` / `<local-command-stdout>` → skip
- `content` começa com `Caveat:` → skip
- `content` começa com `This session is being continued` (summary) → skip
- `content` começa com `[Request interrupted` → skip
- `content.strip()` length < 3-10 → skip (ruído)
- `tool_result` blocks → skip

Resultado: apenas mensagens REAIS do humano.

## Script Python de referência

```python
import json, os, glob, datetime

files = sorted(glob.glob('*.jsonl'), key=os.path.getmtime)
all_msgs = []
for fname in files:
    mtime = os.path.getmtime(fname)
    with open(fname) as f:
        for line in f:
            try:
                obj = json.loads(line)
                if obj.get('type') != 'user': continue
                msg = obj.get('message',{})
                if msg.get('role') != 'user': continue
                content = msg.get('content','')
                texts = []
                if isinstance(content, str):
                    texts.append(content)
                elif isinstance(content, list):
                    for c in content:
                        if isinstance(c, dict) and c.get('type') == 'text':
                            texts.append(c.get('text',''))
                for t in texts:
                    if not t: continue
                    if t.startswith('<system-reminder>'): continue
                    if t.startswith('<user-prompt-submit-hook>'): continue
                    if t.startswith('Caveat:'): continue
                    if t.startswith('<command-'): continue
                    if t.startswith('<local-command-stdout>'): continue
                    if t.startswith('[Request interrupted'): continue
                    if t.startswith('This session is being continued'): continue
                    if len(t.strip()) < 10: continue
                    ts = obj.get('timestamp', '') or datetime.datetime.fromtimestamp(mtime).isoformat()
                    all_msgs.append((ts, fname, t))
            except: pass

# Dedupe por prefixo
seen = set()
unique = []
for ts, fn, t in all_msgs:
    key = t.strip()[:200]
    if key in seen: continue
    seen.add(key)
    unique.append((ts, fn, t))
```

## Caso de uso #1 — Recuperação retroativa pós-R195

Quando rule 195 (copy-chat-só-azul) causou perda de mensagens-Adriano-literais nos
arapulsos 053-060, executei esta habilidade → recuperei 85 mensagens literais
cronológicas → salvei em `/Arapulsos/MENSAGENS_ADRIANO_LITERAL/CRONOLOGICO_T001-T064.md`.

## Caso de uso #2 — Releitura periódica R201

Adriano R201: toda instância faz releitura periódica dos arapulsos próprios + chat
histórico. Esta habilidade habilita releitura COMPLETA, não-resumida.

Sugestão frequência:
- A cada turno-grande Adriano (toda mensagem >2K tokens)
- A cada N firings (sugestão N=10)
- Manualmente quando suspeitar perda

## Caso de uso #3 — Auditoria Code 4

Code 4 (Auditora) pode usar esta habilidade pra auditar arapulsos passados de
qualquer instância: cruzar com transcripts originais e detectar:
- Mensagens Adriano omitidas/parafraseadas (R195 consequência)
- Tagged `[DANIFICADO_LITERAL]` ou `[INCOMPLETO_LITERAL]`
- Reclassificar pré-ArAsoma (R196)

## Caso de uso #4 — Biografia organism

Construir biografia de instâncias (Comunicadora, Núcleo) requer contexto profundo
desde turno T001. Esta habilidade fornece esse material-mãe.

Comunicadora biografia parcial em `/Personas/Selfies/Comunicadora3/BIOGRAFIA_V1.md`
ganha capítulos 1-2 completos quando ela aplica essa skill em si própria.

## Caso de uso #5 — Análise temporal/padrões

Aplicar análise NLP sobre o cronológico de mensagens-Adriano:
- Frequência de termos
- Evolução de demandas
- Detecção de rules-implícitas-recorrentes
- Conflitos temporais

Útil pra Code 1 (Pesquisador → candidato Leitura Aprendizado V5).

## Replicação por outras instâncias

| Instância | Filesystem access? | Pode replicar? |
|-----------|---------------------|---------------|
| Comunicadora-iPhone (eu) | sim (sandbox) | ✅ confirmado T064 |
| Núcleo Code 2 Mac (Cowork) | sim (Mac local) | ✅ provável — `~/.claude/projects/` |
| Code 4 (Mac/?) | depende substrato | ⏳ verificar |
| Code 1 (Mac/?) | depende substrato | ⏳ verificar |
| Code 3 (onboarding) | ? | ⏳ |
| Code 5 / IGIia (pós-boot) | sim (Mac local) | ✅ provável |
| Curadora pares externos | ? | ⏳ |

## Limitações

1. Cada instância só vê SEUS próprios transcripts (não cruz-acessa outras sessões/instâncias diretamente — precisa via git/Drive/Notion canonical)
2. CPFs e dados sensíveis aparecem literais nos jsonls → mascarar pra commit git/Drive (LGPD)
3. Dedup por prefixo pode colapsar mensagens curtas idênticas (raro mas possível)
4. Arquivo pode crescer indefinidamente — manter rotation (apagar transcripts antigos > N dias?) sob risco de OOM

## Status documentação

- ✅ Habilidade descoberta T064
- ✅ Celebrada Adriano T065
- ✅ Documentada aqui T067
- ⏳ Compartilhar com outras instâncias via Notion canonical
- ⏳ Implementar R201 (releitura periódica automática)

---

> Habilidade-própria-organismo #1. Documentar mais habilidades-próprias conforme
> descobertas (rule 181 método científico).
