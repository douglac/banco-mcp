# Instalação detalhada

O Banco MCP é um servidor MCP remoto. Você não precisa rodar nada localmente — só apontar seu cliente pra `https://api.mcp.ai/banco`.

Para os snippets rápidos com deeplinks, veja [INSTALL.md](../INSTALL.md). Esta página explica o fluxo completo, incluindo cadastro, conexão de bancos e troubleshooting.

## Passo 1 — Crie sua conta

1. Acesse [app.mcp.ai/banco](https://app.mcp.ai/banco)
2. Digite seu e-mail
3. Receba o magic-link no inbox e clique pra entrar (não tem senha — autenticação é só por magic-link)

## Passo 2 — Conecte seus bancos

1. Dentro da sua conta, clique em **Conectar banco**
2. Selecione o banco (Itaú, Nubank, Bradesco, Santander, Inter, BB, Caixa, C6, etc.)
3. Você é redirecionado pro fluxo de Open Finance do banco
4. Autentique no app/internet banking
5. Conceda o **consentimento Open Finance** (somente leitura, revogável a qualquer momento)
6. Pronto — o banco aparece como conectado

Você pode conectar **vários bancos**. Cada um vira uma "conexão". O plano que você assina determina quantas conexões pode ter (veja [precos.md](precos.md)).

## Passo 3 — Configure seu cliente MCP

Veja [INSTALL.md](../INSTALL.md) — Cursor, Claude Desktop ou VS Code.

Resumindo:

| Cliente | URL | Auth |
|---|---|---|
| Claude Desktop | `https://api.mcp.ai/banco` | OAuth 2.1 (auto) ou agent-auth |
| Cursor | `https://api.mcp.ai/banco` | OAuth 2.1 (auto) ou agent-auth |
| VS Code (Copilot) | `https://api.mcp.ai/banco` | OAuth 2.1 (auto) ou agent-auth |
| Outros (Continue, Cline, etc.) | `https://api.mcp.ai/banco` | OAuth 2.1 (auto) ou agent-auth |

A configuração JSON é **a mesma em todos**: só a URL, sem headers. O fluxo de auth (OAuth ou agent-auth) é escolhido em runtime pelo cliente.

## Passo 4 — Use no chat

Exemplos de prompts:

- "Liste meus bancos conectados"
- "Quanto gastei esse mês com restaurantes?"
- "Resuma minhas faturas de cartão em aberto"
- "Como está minha carteira de investimentos hoje?"
- "Mostra meu extrato dos últimos 30 dias"

Veja [ferramentas.md](ferramentas.md) pra lista completa de 13 ferramentas.

## OAuth 2.1 vs agent-auth — qual escolher?

Você não escolhe — o cliente escolhe baseado na própria capacidade. A configuração JSON é a mesma:

```json
{"mcpServers": {"banco": {"url": "https://api.mcp.ai/banco"}}}
```

O servidor responde diferente conforme detecta:

**OAuth 2.1 (recomendado, cliente moderno)**:
- Servidor responde 401 com `WWW-Authenticate` apontando pro authorization server
- Cliente abre browser, magic-link, conecta bancos
- Você nunca cola um token
- Token rotaciona sozinho
- Suportado por: Claude Desktop / Cursor / VS Code recentes

**agent-auth (cliente sem OAuth)**:
- Servidor expõe a tool `authenticate(token)` pro agente
- Agente pede pra você abrir `https://app.mcp.ai/agent-auth`
- Você faz login, copia o JWT, cola no chat
- Agente chama `authenticate(token=<JWT>)`
- Pra clientes legados, scripts, CI/CD, agentes próprios
- ⚠️ Token passa pelo chat → entra nos logs do provedor LLM (Anthropic / OpenAI / etc.)

Detalhe completo: [autenticacao.md](autenticacao.md)

## Quanto custa?

- **Free trial**: 10 perguntas em 24h, conexões ilimitadas. Sem cartão.
- **Planos pagos**: a partir de R$ 19,90/mês. Veja [precos.md](precos.md).

## Desinstalar

Pra remover o MCP:

1. **Cliente MCP**: remova o bloco `mcpServers.banco` (ou `servers.banco` no VS Code) do config e reinicie.
2. **Bancos conectados**: vá em `app.mcp.ai/banco` → pra cada banco, **Desconectar** revoga o consentimento Open Finance.
3. **Conta**: `app.mcp.ai/banco → Settings → Excluir conta` apaga tudo (30 dias de retenção legal antes do delete final).

## Troubleshooting

| Sintoma | Causa provável | Solução |
|---|---|---|
| "Authentication required" | OAuth não completou | No browser, finalize o magic-link (Caminho A) ou cole token de `app.mcp.ai/agent-auth` (Caminho B) |
| "401 invalid token" / "Token expired" | JWT expirou | Gere outro em `app.mcp.ai/agent-auth` |
| "Tool authenticate not available" | Cliente fez OAuth com sucesso | Tudo certo — não precisa de agent-auth |
| "Tools não aparecem" | Cliente não recarregou | Reinicie Claude Desktop / Cursor / VS Code |
| "0 bancos conectados" | Não conectou nenhum banco | `app.mcp.ai/banco → Conectar banco` |
| "LOGIN_ERROR no banco" | Open Finance pediu reauth | `app.mcp.ai/banco → Reconectar` no banco afetado |
| "Connection timeout" | Rede/firewall | Verifique se `https://api.mcp.ai/banco` abre no browser |

Dúvidas? [banco@mcp.ai](mailto:banco@mcp.ai)
