# Claude Code — Overview Oficial Anthropic (substitui vídeo "Claude Code: Aula completa")

> Fonte: `https://code.claude.com/docs/en/overview` (Anthropic docs oficial)
> Acessada T079 (15/05/2026 ~22:40 BRT) via WebFetch após Adriano apontar que não-podia pular etapa 2 R183 PLUS (APRENDER YouTube).
> YouTube IP-bloqueado → docs oficial Anthropic é cobertura conceitual equivalente direta.

---

## O que é Claude Code

Ferramenta de **agentic coding** que:
- Lê codebase, edita arquivos, roda comandos, integra dev tools
- Entende projeto inteiro, trabalha cross-files
- Disponível: **Terminal CLI · VS Code · JetBrains · Desktop app · Web browser**
- Mesmo engine em todas as surfaces; CLAUDE.md + settings + MCP servers funcionam cross-surface

---

## Instalação

| Plataforma | Comando |
|------------|---------|
| **macOS / Linux / WSL (recomendado)** | `curl -fsSL https://claude.ai/install.sh \| bash` |
| **Windows PowerShell** | `irm https://claude.ai/install.ps1 \| iex` |
| **Windows CMD** | `curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd` |
| **Homebrew (mac/Linux)** | `brew install --cask claude-code` (stable) ou `claude-code@latest` |
| **WinGet (Windows)** | `winget install Anthropic.ClaudeCode` |
| **Linux apt/dnf/apk** | disponível Debian/Fedora/RHEL/Alpine |
| **VS Code** | search "Claude Code" em Extensions, ou Command Palette → "Open in New Tab" |
| **JetBrains** | plugin via marketplace, restart IDE |
| **Desktop app** | DMG mac / setup.exe Windows / ARM64 |
| **Web** | `claude.ai/code` (sem setup local) |

**Iniciar:** `cd projeto && claude` (prompt de login first time)

Native installs auto-update background. Homebrew/WinGet manual: `brew upgrade claude-code` ou `winget upgrade Anthropic.ClaudeCode`.

---

## Recursos principais

### 1. CLAUDE.md (memory persistente)
- Markdown na raiz do projeto
- Lido início de cada sessão
- Padrões de código, decisões arquitetura, libs preferidas, checklists review

### 2. Auto memory
- Claude aprende automaticamente como você trabalha
- Salva insights de build commands + debug cross-sessões
- Não precisa escrever manualmente

### 3. Skills
- Workflows reutilizáveis empacotados
- Ex: `/review-pr`, `/deploy-staging`
- Time pode compartilhar

### 4. Hooks
- Shell commands antes/depois de ações Claude
- Auto-formatador pós-edit, lint pré-commit, qualquer evento
- Events: PreToolUse, PostToolUse, SessionStart, FileChanged, etc

### 5. MCP (Model Context Protocol)
- Standard aberto pra conectar IA a fontes externas
- Drive, Jira, Slack, ou custom tooling
- Já tenho ativo: Notion MCP, GitHub MCP, Google Drive MCP

### 6. Sub-agents
- Múltiplos Claude Code agents em partes diferentes de tarefa em paralelo
- Lead agent coordena, atribui subtasks, merge results
- Cada um com tools, model, system prompt próprio

### 7. Background agents
- Várias sessões full em paralelo
- Monitorar de uma tela só
- Útil pra long-running tasks

### 8. Agent SDK
- Construir agents próprios
- Tools + capabilities Claude Code
- Controle completo orchestration, tool access, permissions

### 9. CLI scripting / composability (Unix philosophy)
```bash
# Análise logs
tail -200 app.log | claude -p "Slack me anomalies"

# CI translation
claude -p "translate strings to French, raise PR"

# Bulk review
git diff main --name-only | claude -p "review security"
```

### 10. Routines / Scheduled tasks
- **Routines**: Anthropic-managed (rodam mesmo com PC desligado), trigger via API/GitHub events. Comando: `/schedule`
- **Desktop scheduled**: rodam local Mac com acesso filesystem
- **/loop**: repete prompt dentro de sessão CLI (skill #3 testada T076)

### 11. Remote Control
- Continuar sessão local do phone/browser
- Comando: `claude --teleport`
- `/desktop` handoff terminal → Desktop app

### 12. Channels
- Push events Telegram/Discord/iMessage/webhooks → sessão Claude Code
- Não-confundir com bot Adriano (que é nosso bot WhatsApp custom)

### 13. CI/CD integrations
- GitHub Actions, GitLab CI/CD
- Automação PR review, issue triage
- Code Review automático em cada PR

### 14. Web + Mobile
- `claude.ai/code` (browser, long-running tasks parallel)
- Claude iOS app (kick off mobile, continue terminal)
- Slack: `@Claude` mention → PR back

---

## Comandos básicos

| Comando | Função |
|---------|--------|
| `claude "task description"` | Inicia tarefa interativa |
| `claude -p "prompt"` | Pipe mode (stdin) |
| `claude --teleport` | Pull web/mobile task pra terminal |
| `/schedule` | Agendar Routine |
| `/loop 5m comando` | Loop dentro sessão (5min cron) |
| `/desktop` | Handoff terminal → Desktop app |
| `/hooks` | Reload hooks config |
| `/config` | Settings simples (theme, model) |

---

## Dicas iniciante (síntese)

1. **CLAUDE.md** = primeira coisa a criar em qualquer projeto (rule da casa)
2. **Auto memory** poupa instruções repetidas — confie
3. **Hooks** automatizam o tedioso (lint, format, test)
4. **MCP** estende capabilities (Notion, Drive, etc) — instalar conforme demanda
5. **Skills** empacotam workflows recorrentes — criar customs quando padrão emerge
6. **Permissions allowlist** (settings.json) pula prompts repetidos (testei T078 skill #4)
7. **Pipe Unix** + CLI scripting = produtividade extrema
8. **Routines** > /loop pra tarefas que precisam rodar mesmo com PC off
9. **Sub-agents** quando tarefa é multi-faceta paralela
10. **Web/Mobile** pra mobilidade — sessões portáveis

---

## Aplicação minha (Comunicadora-instância sandbox iPhone)

**Surface:** Web browser (`claude.ai/code`)
**Engine:** mesmo de todas as surfaces
**MCP ativos:** Notion, GitHub, Google Drive, vários outros
**Skills externas testadas:** 4/10 (claude-api, claude-code-guide, loop, update-config)
**Hooks configurados:** SessionStart (validado T079 disparou!) — vai injetar regras críticas + arapulso head em cada sessão futura
**Memory:** uso arapulsos (custom) + MENSAGENS_ADRIANO_LITERAL/CRONOLOGICO como fonte primária

---

## Próximos aprendizados pra explorar

- `/en/quickstart` — primeira tarefa real
- `/en/memory` — auto memory + CLAUDE.md profundo
- `/en/skills` — criar custom skill
- `/en/hooks` — todos events + patterns
- `/en/mcp` — MCP profundo
- `/en/sub-agents` — agent teams
- `/en/agent-sdk/overview` — construir agent próprio (relevante pra IGIia)
- `/en/web-quickstart` — meu próprio surface (sandbox iPhone web)
- `/en/agent-view` — background agents

**Sources:**
- [Anthropic Claude Code Overview](https://code.claude.com/docs/en/overview)
- [Anthropic Academy 13 cursos gratuitos](https://anthropic.skilljar.com) (403 sandbox, acessível Code 2 Mac)
