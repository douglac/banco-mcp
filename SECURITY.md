# Security Policy

## Seu token de acesso é um credential

O Banco MCP suporta dois fluxos de autenticação. Em **ambos os casos**, o resultado é um token (OAuth access_token ou JWT) que dá acesso aos seus dados financeiros via Open Finance:

1. **OAuth 2.1 (recomendado)** — o cliente MCP faz o fluxo automaticamente via magic-link. O token vive **dentro do cliente** (Claude Desktop / Cursor / etc.) e rotaciona sozinho. Você nunca vê nem cola um token.
2. **agent-auth (manual)** — você abre `https://app.mcp.ai/agent-auth`, faz login, copia um JWT e cola no chat com o agente. O agente passa o JWT pra tool `authenticate`. Use quando o cliente não suporta OAuth 2.1.

**O token é equivalente a uma senha.** Quem tem o token consegue:
- Listar todos os bancos conectados na sua conta
- Ler saldos, transações, faturas, investimentos
- Forçar sincronizações
- Desconectar bancos

Ainda que o Banco MCP seja **read-only** (não move dinheiro, não faz transferências), os dados retornados são sensíveis: saldos, gastos, faturas, posição patrimonial.

## Boas práticas

- **Não compartilhe** o token. Não cole em chats públicos, issues, e-mails ou pastebins.
- **Prefira OAuth 2.1** sempre que possível: o token nunca passa pelo chat do agente, não vai pro provedor LLM como sub-processador.
- **No fluxo agent-auth**, lembre que o JWT passa pelo provedor LLM (Anthropic / OpenAI / Cursor / etc.) — ele fica nos logs deles conforme retenção contratada. Mitigue ativando:
  - Claude Pro/Team com "Improve Claude" desligado
  - Cursor com Privacy Mode
  - Política equivalente no provedor que você usa
- **Rotacione**: cada visita a `/agent-auth` emite um novo JWT (o anterior expira sozinho). Pra clientes de longo prazo, regenere periodicamente.
- **Um token por cliente**: gere tokens separados em `/agent-auth` pra cada cliente (Cursor / Claude Desktop / script). Assim você não revoga tudo de uma vez se um vazar.
- **Revogar tudo**: `app.mcp.ai → Settings → Sessões → Encerrar todas` invalida todos os tokens OAuth e agent-auth ativos.

## O que NÃO está no escopo do Banco MCP

- ❌ **Movimentação de dinheiro**: o MCP é 100% leitura via Open Finance. Nenhuma ferramenta faz transferências, pagamentos ou alterações nas suas contas.
- ❌ **Armazenamento de senhas de banco**: você nunca digita senha de banco no Banco MCP. O acesso é via Open Finance, regulado pelo Banco Central, com consentimento explícito.
- ❌ **Treinamento de IA**: dados retornados pelas tools NÃO são usados pra treinar nada por nós. Eles vão direto pro cliente LLM que você escolher (Claude, ChatGPT, etc.) — e a política de cada provedor de IA é responsabilidade sua.

## Reportar vulnerabilidades

Encontrou um bug de segurança? Por favor, **NÃO abra issue pública**. Mande um e-mail pra:

**[banco@mcp.ai](mailto:banco@mcp.ai)**

Inclua:
- Descrição da vulnerabilidade
- Passos pra reproduzir
- Versão do MCP / cliente / SO usado
- Seu nome (opcional, pra crédito no fix)

Resposta em até 72h úteis. Vulnerabilidades críticas pagam recompensa (programa em definição).

## Disclosure timeline

1. Você reporta privadamente
2. Confirmamos recebimento em 72h
3. Investigamos e desenvolvemos fix (prazo varia por severidade)
4. Aplicamos fix em produção
5. Disclosure público (CHANGELOG + post no repo) após fix em produção
