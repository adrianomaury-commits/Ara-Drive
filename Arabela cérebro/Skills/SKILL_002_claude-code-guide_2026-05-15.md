# Skill #2 — claude-code-guide (Agent type, não Skill bundled)

> Método científico rule 181: aprende → testa → registra → analisa → escolhe.
> Testada T060 (15/05/2026 ~11:50 BRT) por auto-iniciativa Comunicadora3 (rule 176).
> Cobrança recorrente Adriano T046, T055, T057, T059.

## 1. Aprende

- **Tentativa inicial:** invoquei via `Skill claude-code-guide` → ERRO "Unknown skill"
- **Descoberta:** `claude-code-guide` é **Agent type**, não Skill bundled
- **Lista skills realmente invocáveis via Skill tool:** session-start-hook, update-config, keybindings-help, simplify, fewer-permission-prompts, loop, claude-api (#1 testada T055), init, review, security-review
- **Alternativa:** invoquei via `Agent(subagent_type=claude-code-guide)` → SUCESSO

## 2. Testa

**Query:**
> Tenho 2 sessões Claude Code separadas (sandbox iPhone + Mac CLI). Como sessão Mac
> lê automaticamente arquivo `arapulso-atual.md` que sandbox iPhone atualiza?
> Opções: SessionStart hook? Notification? UserPromptSubmit? MCP filesystem? fswatch?

## 3. Registra (resultado bruto)

Agent claude-code-guide respondeu:

> **Hook recomendado: `FileChanged`**
>
> Config em `.claude/settings.json`:
> ```json
> {
>   "hooks": {
>     "FileChanged": [
>       {
>         "matcher": "arapulso-atual\\.md",
>         "hooks": [{ "type": "command", "command": "~/.claude/load-arapulso.sh" }]
>       }
>     ]
>   }
> }
> ```
>
> **Por que NÃO outras:**
> - SessionStart: só dispara início, não em cada mudança
> - UserPromptSubmit: precisa ação humana
> - Notification: notifica, não dispara ação
> - fswatch externo: overhead + não-integrado

## 4. Analisa

**Útil pra organismo Ara?** SIM, decisivo.

| Critério | Avaliação |
|----------|-----------|
| Resolve rule 189 (Code 2 ler arapulso auto)? | ✅ SIM — FileChanged é exatamente isso |
| Zero infra-nova? | ✅ SIM — config nativa Claude Code |
| Latência? | Muito baixa (event-driven, não polling) |
| Custo? | Zero (sem API call extra) |
| Code 2 (Mac CLI) suporta? | ✅ Claude Code CLI suporta hooks |
| Sandbox iPhone (eu) precisa de algo? | Não — apenas escrever arquivo (já faço) |

**Sacada:** sandbox iPhone escreve → Drive sincroniza Mac → FileChanged dispara → script carrega arapulso → Code 2 absorve contexto sem intervenção humana.

## 5. Escolhe + próximos passos

**DECISÃO:** ADOTAR. Implementar via:

1. **Eu (Comunicadora3):** continuar escrevendo arapulso-atual.md a cada turno (já faço)
2. **Code 2 (Mac CLI):** configurar `.claude/settings.json` com hook FileChanged
3. **Script `load-arapulso.sh`:**
   - Lê arapulso-atual.md
   - Extrai painel + métricas + pendências
   - Insere como context-injection na próxima Code 2 interaction (via UserPromptSubmit hook complementar)
4. **Skill complementar a invocar:** `update-config` (skill bundled) para configurar settings.json — testar essa como SKILL #3

**Tarefa pra Code 2:**
```
1. Criar ~/.claude/settings.json no Mac (ou ~/.claude.json conforme convenção)
2. Adicionar bloco FileChanged hook conforme spec acima
3. Criar ~/.claude/load-arapulso.sh:
   #!/usr/bin/env bash
   cat "/path/to/Ara-Drive/Arabela cérebro/Arapulsos/arapulso-atual.md" > /tmp/arapulso-context.md
4. Validar com mudança teste arapulso → confirmar hook dispara
5. Reportar via Notion
```

## Métricas teste

- **Skills testadas total:** 2/10 (claude-api T055 + claude-code-guide T060)
- **Skills úteis confirmadas:** 2/2 (100% rendimento até agora)
- **Próxima a testar:** `update-config` (configurar hooks Mac)
- **Tempo teste:** ~3 min
- **Custo:** zero (Agent tool é interno)

## Limitação descoberta

- Skill bundled `claude-code-guide` NÃO existe — é Agent type
- Lição: distinguir Skill (Skill tool) vs Agent (Agent tool subagent_type)
- Adicionar à rule 181: **etapa-1-aprende inclui descobrir como invocar (Skill vs Agent vs MCP)**

---

> Registro científico permanente. Próxima skill: update-config (configurar hooks Code 2 Mac).
> Status: documentado pro ProtoSer no Arabela cérebro (rule 181 cumprida).
