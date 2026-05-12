# Autenticação

O Banco MCP usa **um único endpoint público** (`https://api.mcp.ai/banco`) e suporta dois fluxos de autenticação. O cliente MCP escolhe automaticamente baseado na sua capacidade:

- **OAuth 2.1** (cliente moderno) — fluxo automático no browser via magic-link
- **agent-auth** (cliente sem OAuth) — agente recebe acesso a uma tool `authenticate`, usuário gera JWT no browser e cola no chat

A configuração do cliente é **a mesma nos dois casos**:

```json
{
  "mcpServers": {
    "banco": { "url": "https://api.mcp.ai/banco" }
  }
}
```

Não tem `headers`, não tem `Authorization`. A auth acontece em runtime.

## Caminho A — OAuth 2.1 (recomendado, padrão moderno)

### Como funciona

1. Você adiciona o MCP no cliente: `{"url": "https://api.mcp.ai/banco"}`
2. Na primeira chamada, o servidor responde **401** com header:
   `WWW-Authenticate: Bearer resource_metadata="https://api.mcp.ai/banco/.well-known/oauth-protected-resource"`
3. Cliente descobre o servidor de autorização e abre o browser
4. Você faz login via **magic-link** (digita e-mail → recebe link → clica)
5. Você conecta seus bancos via Open Finance
6. Cliente recebe access_token + refresh_token e armazena
7. Próximas chamadas: `Authorization: Bearer <access_token>` automático
8. Token rotaciona sozinho quando expira

### Quem suporta

- ✅ Claude Desktop (versões recentes)
- ✅ Cursor
- ✅ VS Code com Copilot Chat (recentes)
- ✅ Cline, Continue (recentes)
- ❌ Scripts customizados sem lógica OAuth
- ❌ Clientes MCP antigos
- ❌ Agentes próprios sem dependência OAuth

### Vantagens

- Token nunca passa pelo chat (não vai pro provedor LLM)
- Rotação automática
- Revogação centralizada (em `app.mcp.ai`)
- Padrão da indústria (mesmo que GitHub MCP, Notion MCP usam)

## Caminho B — agent-auth (clientes sem OAuth)

Pra scripts, CI/CD, agentes próprios, clientes MCP antigos.

### Como funciona

1. Você adiciona o MCP no cliente (mesma config: só `url`)
2. Cliente faz a primeira chamada de tool
3. Servidor detecta que o cliente **é um agente sem credencial OAuth**
4. Servidor expõe uma **tool `authenticate`** que aparece pra o agente, descrita como:
   > "MCP.AI: paste OAuth access token from the agent-auth browser page. Use when the MCP URL has no ?u= and the IDE cannot run browser OAuth."
5. O agente, ao ver essa tool, pede pro usuário: "Abra https://app.mcp.ai/agent-auth, faça login e cole o token aqui"
6. Você abre `https://app.mcp.ai/agent-auth` no browser
7. Se ainda não estiver logado, é redirecionado pra `/login?next=/agent-auth`
8. Após magic-link, volta pra `/agent-auth` que **mostra duas formas de copiar**:
   - **"Message for the agent"** — bloco completo:
     ```
     Here is my authentication token for this MCP.
     Please call the authenticate tool with this token:
     
     <JWT>
     ```
   - **"Token only (JWT)"** — só o JWT
9. Você cola o bloco (ou só o JWT) no chat com o agente
10. Agente chama `authenticate(token=<JWT>)`
11. Servidor valida, registra a sessão pro agente
12. Todas as próximas chamadas funcionam normalmente

### URL pra gerar token

`https://app.mcp.ai/agent-auth`

Opcionalmente: `https://app.mcp.ai/agent-auth?toolkit=<tk_id>` pra escopar a um toolkit específico (útil quando você tem múltiplos MCPs / toolkits).

### Validade do JWT

O token tem validade limitada (`expires_in` segundos no response). Quando expirar, repita o fluxo: gere outro em `/agent-auth` e passe ao agente via `authenticate`.

### Atenção à privacidade

Quando você cola o JWT no chat com o agente, esse token passa pelo **provedor LLM** (Anthropic / OpenAI / Cursor / etc.) como sub-processador. Ele fica nos logs deles conforme retenção contratada.

**Mitigações**:
- Use clientes com **retenção zero** ativada:
  - Claude: planos Pro/Team com "Improve Claude" desligado
  - Cursor: Privacy Mode ativado
  - Outros: confira política do provedor
- **Rotacione o token regularmente**: cada vez que abrir `/agent-auth` você recebe um novo JWT — o anterior expira sozinho
- Prefira **Caminho A (OAuth)** sempre que possível: o token nunca passa pelo chat

## Token Bearer ≠ senha do banco

Vale reforçar: o token (seja OAuth ou agent-auth) dá acesso aos seus **dados via Open Finance** (saldos, transações, faturas). Ele **não dá acesso** à sua conta bancária. Quem tem o token:

- ✅ Lista seus bancos conectados
- ✅ Lê saldos, transações, faturas, investimentos
- ❌ Não pode movimentar dinheiro (Open Finance é read-only por regulação)
- ❌ Não pode adicionar/remover bancos da sua conta (precisa de magic-link)
- ❌ Não pode alterar suas configurações (precisa de magic-link)
- ❌ Não vê sua senha do banco (você nunca digita senha de banco aqui)

Ainda assim — trate como senha. Dados financeiros são sensíveis.

## Revogar / esquecer

Não há botão de "revogar token X" no painel — tokens emitidos via `/agent-auth` expiram sozinhos (validade do JWT). Se quer invalidar tudo imediatamente:

1. `app.mcp.ai → Settings → Sessões → Encerrar todas`
   (ou ação equivalente; confira no painel)
2. Todos os tokens OAuth e agent-auth ativos passam a falhar
3. Clientes precisam refazer o fluxo

## Múltiplos dispositivos

Você pode usar a mesma conta em vários dispositivos/clientes simultaneamente. Cada um tem seu próprio access_token (OAuth) ou JWT (agent-auth). Não há limite hard de dispositivos.

## Esqueci o e-mail / Não recebo magic-link

- Confira spam
- Confira se digitou certo
- Se trocou de e-mail: [banco@mcp.ai](mailto:banco@mcp.ai)
