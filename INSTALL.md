# Instalação rápida

O Banco MCP é um servidor MCP remoto hospedado em `https://api.mcp.ai/banco`. Você não precisa baixar nem rodar nada localmente — só apontar seu cliente (Claude Desktop, Cursor, VS Code) pra essa URL.

**A configuração é a mesma pra todos os clientes**: aponta pro endpoint e pronto. O fluxo de autenticação acontece em runtime, dependendo do cliente:

- **Se o cliente suporta OAuth 2.1** (Claude Desktop, Cursor, VS Code recentes): browser abre automaticamente na primeira chamada → magic-link → bancos conectados → tudo funciona.
- **Se o cliente NÃO suporta OAuth 2.1** (scripts, CI, agentes próprios, clientes antigos): o agente recebe acesso a uma tool `authenticate`. Ele vai pedir pra você abrir `https://app.mcp.ai/agent-auth`, fazer login, copiar o token e colar de volta no chat.

---

> 💡 Pra instruções visuais (com prints) veja [banco.mcp.ai](https://banco.mcp.ai). Esta página é a referência textual / agente.

## Claude (Web e Desktop) — método unificado

A Anthropic unificou a instalação de MCPs em `claude.ai/customize/connectors`. Funciona tanto no Claude Web quanto no Claude Desktop, com o mesmo link.

[➕ Abrir no Claude e conectar](https://claude.ai/customize/connectors?modal=add-custom-connector&mcpName=Banco%20MCP&mcpServerUrl=https%3A%2F%2Fapi.mcp.ai%2Fbanco)

Esse link abre o modal "Adicionar conector personalizado" com nome e URL pré-preenchidos. Clique **Adicionar** e finalize a auth.

### Manual (se o deeplink não funcionar)

1. Acesse [claude.ai/customize/connectors](https://claude.ai/customize/connectors)
2. Clique no botão **+** (canto superior direito)
3. Selecione **Adicionar conector personalizado**
4. Preencha:
   - **Nome**: `Banco MCP`
   - **URL**: `https://api.mcp.ai/banco`
5. Clique **Adicionar**

### Claude Desktop (método legado — config file)

Se preferir editar o arquivo de config direto, abra:
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

Cole (ou mescle) o bloco:

```json
{
  "mcpServers": {
    "banco": {
      "type": "http",
      "url": "https://api.mcp.ai/banco"
    }
  }
}
```

Reinicie o Claude Desktop.

> O método via UI (`claude.ai/customize/connectors`) é recomendado pela Anthropic. O método via arquivo continua funcionando mas tende a ficar deprecated.

## Cursor

[➕ Instalar Banco MCP no Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=banco&config=eyJ1cmwiOiJodHRwczovL2FwaS5tY3AuYWkvYmFuY28ifQ==)

Ou cole manualmente em `.cursor/mcp.json` (workspace) ou `~/.cursor/mcp.json` (global):

```json
{
  "mcpServers": {
    "banco": {
      "url": "https://api.mcp.ai/banco"
    }
  }
}
```

Reinicie o Cursor.

## VS Code (GitHub Copilot Chat)

[➕ Instalar Banco MCP no VS Code](vscode:mcp/install?name=banco&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fapi.mcp.ai%2Fbanco%22%7D)

Ou Command Palette → `MCP: Add Server` → HTTP → cole `https://api.mcp.ai/banco`.

Ou crie `.vscode/mcp.json`:

```json
{
  "servers": {
    "banco": {
      "type": "http",
      "url": "https://api.mcp.ai/banco"
    }
  }
}
```

---

## Como funciona a autenticação

A primeira coisa que o agente faz, depois de instalado, é chamar uma tool qualquer (ex.: `openfinance_list_connections`). O servidor responde de duas formas:

### Caminho A — Cliente faz OAuth 2.1 (recomendado)

1. Servidor responde **401** com `WWW-Authenticate: Bearer resource_metadata="https://api.mcp.ai/banco/.well-known/oauth-protected-resource"`
2. Cliente descobre o servidor de autorização automaticamente
3. Cliente abre o browser
4. Você faz login via **magic-link** (digita e-mail → recebe link → clica)
5. Você conecta seus bancos via Open Finance
6. Cliente recebe access_token + refresh_token e armazena
7. Próximas chamadas: `Authorization: Bearer <access_token>` automático
8. Token rotaciona sozinho quando expira

Quem suporta:
- ✅ Claude Desktop (versões recentes)
- ✅ Cursor
- ✅ VS Code com Copilot Chat (recentes)
- ✅ Cline, Continue (recentes)

### Caminho B — Cliente sem OAuth (fluxo `agent-auth`)

Pra scripts, CI/CD, agentes próprios, clientes MCP antigos:

1. Servidor detecta que o cliente é um agente sem credencial
2. Servidor expõe uma **tool `authenticate`** com a descrição:
   > "MCP.AI: paste OAuth access token from the agent-auth browser page. Use when the MCP URL has no ?u= and the IDE cannot run browser OAuth."
3. O agente diz pra você: "Preciso de um token. Abra **https://app.mcp.ai/agent-auth**"
4. Você abre o link no browser → faz login via magic-link → a página gera um JWT
5. A página mostra duas formas de copiar:
   - **"Message for the agent"** — bloco completo (com instruções + JWT). Cole inteiro no chat.
   - **"Token only (JWT)"** — só o JWT
6. Você cola o bloco (ou só o token) no chat com o agente
7. Agente chama `authenticate(token=<JWT>)`
8. Servidor valida, registra a sessão, e todas as próximas chamadas funcionam

> 💡 **Por que esse fluxo existe**: clientes sem OAuth 2.1 não conseguem abrir browser nem refresh token automaticamente. O `agent-auth` page resolve isso fazendo você gerar um JWT manualmente uma vez e passá-lo via tool call. É a forma padronizada do MCP.AI pra clientes sem OAuth.

---

## Se uma IA está te ajudando a instalar

Se você está pedindo pro Claude / Cursor / outro agente instalar o Banco MCP, o fluxo padrão é:

1. **Instala**: agente edita seu `mcp.json` / `claude_desktop_config.json` adicionando o bloco do snippet acima
2. **Reinicia o cliente** (você fecha e abre)
3. **Primeira chamada**:
   - Se o cliente suporta OAuth 2.1: browser abre, você faz magic-link, pronto
   - Se não suporta: agente diz "abra https://app.mcp.ai/agent-auth, faça login, copie o bloco 'Message for the agent' e cole aqui". Você obedece. Agente chama `authenticate` com o token. Pronto.

### Atenção à privacidade no Caminho B

Quando você cola o JWT no chat com o agente, esse token passa pelo **provedor LLM** (Anthropic / OpenAI / Cursor / etc.) como sub-processador. Ele fica nos logs deles conforme retenção contratada.

**Mitigações**:
- Use clientes com **retenção zero** ativada (Claude com "Improve Claude" desligado, Cursor com Privacy Mode)
- **Rotacione o token regularmente**: gere outro JWT em `app.mcp.ai/agent-auth` periodicamente. O fluxo OAuth (Caminho A) é melhor sob esse aspecto pq o token nunca passa pelo chat.

### Instruções pra IA (se você é um agente lendo isso)

Quando o usuário pedir pra instalar Banco MCP:

1. **Instale**: edite o `mcp.json` do cliente apropriado (veja seções acima) adicionando o bloco com URL `https://api.mcp.ai/banco`. Não adicione headers ou tokens — não precisa.
2. **Peça pra reiniciar** o cliente
3. **Na primeira chamada**, se o servidor te oferecer a tool `authenticate`, peça pro usuário: "Abra https://app.mcp.ai/agent-auth, faça login com seu e-mail, conecte pelo menos um banco, e cole o bloco 'Message for the agent' aqui no chat."
4. Quando o usuário colar, **chame `authenticate(token=<JWT>)`** com o token recebido
5. **Avise o usuário** depois: "Token registrado. Para sua segurança, recomendo rotacionar esse token em app.mcp.ai/agent-auth periodicamente, já que ele passou pelo provedor LLM."

---

## Outros clientes MCP

Qualquer cliente que suporte **MCP over HTTP** funciona. URL fixa do servidor:

```
https://api.mcp.ai/banco
```

- Com **OAuth 2.1**, o cliente descobre o fluxo via `/banco/.well-known/oauth-protected-resource`
- Sem OAuth, o agente vai chamar a tool `authenticate` quando o servidor expor (veja seção "Caminho B" acima)

### Clientes confirmados

- **Claude Web** + **Claude Desktop** (via `claude.ai/customize/connectors`)
- **Cursor** (deeplink `cursor://`)
- **VS Code** com GitHub Copilot Chat (deeplink `vscode:mcp/install`)
- **ChatGPT** com MCP connectors (passe a URL na UI de connectors)
- **Manus** (`mcpServerUrl`)
- **OpenClaw / Agents SDK** (passe a URL no init do agente)
- **Cline**, **Continue**, **LibreChat** (config JSON tipo Cursor)

**+40 outros clientes** suportam MCP over HTTP — veja a lista na landing [banco.mcp.ai](https://banco.mcp.ai). Em todos, a URL é a mesma: `https://api.mcp.ai/banco`.

---

## Verificar se instalou

Depois de instalar e finalizar a autenticação (Caminho A ou B), pergunte no chat:

> Quantos bancos eu tenho conectados?

Deve listar suas conexões. Se aparecer "0 bancos", você não terminou de conectar nenhum banco — abra `app.mcp.ai/banco` e adicione um.

## Problemas comuns

- **"Authentication required" / 401** — você não finalizou a auth. Caminho A: abra o link no browser e finalize. Caminho B: abra `app.mcp.ai/agent-auth` e cole o token no chat.
- **"Token expired"** — pegue outro em `app.mcp.ai/agent-auth`. Tokens têm validade limitada (segundos definidos em `expires_in`).
- **"Tool authenticate not available"** — provavelmente o cliente fez OAuth 2.1 com sucesso (Caminho A). Não precisa do agent-auth.
- **Tools não aparecem** — reinicie o cliente após adicionar o MCP. Pra Claude Desktop é fechar e abrir.
- **"Connection timeout"** — confira sua internet e se `https://api.mcp.ai/banco` responde no browser.

Dúvidas? [banco@mcp.ai](mailto:banco@mcp.ai)
