# Banco MCP

### Open Finance brasileiro para Claude, Cursor e agentes de IA

Conecte **Itaú, Bradesco, Santander, Nubank, Inter, Banco do Brasil, Caixa, C6** e mais 25 bancos ao seu agente de IA. Pergunte sobre seus gastos, extratos, investimentos e cartões em **linguagem natural**. Open Finance regulado pelo Banco Central, em 30 segundos.

[![Banco MCP — Demo](https://img.youtube.com/vi/cuOEJYbSNnU/maxresdefault.jpg)](https://www.youtube.com/watch?v=cuOEJYbSNnU "▶ Assista no YouTube")

- 🇧🇷 **30+ bancos brasileiros** suportados
- 💬 **Funciona com qualquer cliente MCP**: Claude Desktop, Cursor, VS Code, Cline, Continue
- 🔒 **Somente leitura** — a IA não pode movimentar dinheiro
- 📊 **13 ferramentas**: extrato, saldos, faturas, investimentos, empréstimos
- ⚡ **Open Finance regulado** pelo Banco Central do Brasil
- 🛡️ **LGPD-compliant** — consentimento explícito, retenção mínima

[English version](README.en.md) · [Documentação completa](docs/) · [Skills do Claude Code](skills/)

---

## Instalar em 1 clique

### Claude (Web e Desktop)

A Anthropic unificou a instalação de MCPs em `claude.ai/customize/connectors`. **O mesmo link serve pra Claude Web e Claude Desktop** (basta estar logado na sua conta):

[➕ Abrir no Claude e conectar](https://claude.ai/customize/connectors?modal=add-custom-connector&mcpName=Banco%20MCP&mcpServerUrl=https%3A%2F%2Fapi.mcp.ai%2Fbanco)

Esse link abre o modal "Adicionar conector personalizado" já com **nome** (`Banco MCP`) e **URL** (`https://api.mcp.ai/banco`) pré-preenchidos. Clique **Adicionar**, faça login via magic-link na primeira chamada, conecte seus bancos.

**Manual** (se o deeplink não abrir): vá em [claude.ai/customize/connectors](https://claude.ai/customize/connectors) → clique **+** → **Adicionar conector personalizado** → cole:
- **Nome**: `Banco MCP`
- **URL**: `https://api.mcp.ai/banco`

### Cursor

[➕ Instalar Banco MCP no Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=banco&config=eyJ1cmwiOiJodHRwczovL2FwaS5tY3AuYWkvYmFuY28ifQ==)

### VS Code (Copilot Chat)

[➕ Instalar Banco MCP no VS Code](vscode:mcp/install?name=banco&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fapi.mcp.ai%2Fbanco%22%7D)

### ChatGPT, Manus, OpenClaw e mais 40+ clientes

Banco MCP funciona em qualquer cliente MCP que suporte **MCP over HTTP**. A URL do servidor é sempre a mesma:

```
https://api.mcp.ai/banco
```

Veja [INSTALL.md](INSTALL.md#outros-clientes-mcp) pra exemplos de configuração por cliente, ou [banco.mcp.ai](https://banco.mcp.ai) pra instruções visuais.

**Instruções detalhadas**: [INSTALL.md](INSTALL.md) — config manual em arquivo (`claude_desktop_config.json`, `.cursor/mcp.json`, `.vscode/mcp.json`), fluxo `agent-auth` pra clientes sem OAuth, troubleshooting, e [instruções pra IA te ajudar a instalar](INSTALL.md#se-uma-ia-está-te-ajudando-a-instalar).

---

## Pergunte sobre seu dinheiro

Depois de conectar pelo menos 1 banco, faça perguntas como:

```
Quanto gastei esse mês com restaurantes e mercado?
Liste minhas últimas 20 transações em todas as contas
Resuma minhas faturas de cartão em aberto
Como está minha carteira de investimentos hoje?
Quais foram meus maiores gastos no último trimestre?
Compara minha fatura desse mês com a do mês passado
Quanto tenho em renda fixa vs ações?
Detecta gastos recorrentes nos últimos 6 meses
```

---

## 13 ferramentas disponíveis

| Tool | Descrição |
|---|---|
| `openfinance_list_connections` | Lista bancos conectados |
| `openfinance_get_item_status` | Status de sincronização de uma conexão |
| `openfinance_list_accounts` | Contas correntes, poupanças e cartões |
| `openfinance_get_accounts_detail` | Detalhe completo (limites, cartões adicionais) |
| `openfinance_get_account_balance` | Saldo em tempo real |
| `openfinance_list_transactions` | Extrato com filtros de data e keywords |
| `openfinance_list_credit_card_bills` | Faturas de cartão com `payment_status` derivado |
| `openfinance_get_credit_card_bill` | Detalhe de uma fatura |
| `openfinance_list_investments` | Carteira: FIIs, ações, renda fixa, fundos, previdência |
| `openfinance_list_investment_transactions` | Movimentações de um investimento |
| `openfinance_list_loans` | Contratos de empréstimo |
| `openfinance_list_categories` | Taxonomia de categorias |
| `openfinance_force_sync` | Força sincronização imediata |
| `openfinance_disconnect_bank` | Remove conexão e revoga consentimento |

Documentação detalhada de cada tool: [docs/ferramentas.md](docs/ferramentas.md)

---

## Bancos suportados

Open Finance Brasil cobre as principais instituições:

**Bancos digitais**: Nubank, Inter, C6 Bank, Next, Neon, Original, BMG, PicPay, Mercado Pago, Pan

**Bancos tradicionais**: Itaú, Bradesco, Santander, Banco do Brasil, Caixa Econômica, Safra, Sicoob, Sicredi

**Corretoras**: XP, Rico, Clear, BTG Pactual, Inter Investimentos, Nu Invest, ÓRAMA

**Crédito**: Will Bank, Méliuz, Ame, Renner Card

E mais. Lista completa via tool `openfinance_list_connections` (mostra apenas os disponíveis pra você conectar).

---

## Como funciona

```
1. Você instala o MCP no seu cliente (Claude/Cursor/VS Code)
2. Na primeira pergunta, browser abre pra magic-link
3. Você cria conta (só e-mail, sem senha) e conecta bancos via Open Finance
4. Concede consentimento Open Finance (somente leitura, revogável)
5. Pronto — perguntas em linguagem natural
```

Detalhe técnico: [docs/instalacao.md](docs/instalacao.md) · [docs/autenticacao.md](docs/autenticacao.md)

---

## Skill do Claude Code

Uma skill geral pronta pra Claude Code que cobre **todos os casos de uso** do Banco MCP — análise de gastos, extrato mensal, fluxo de caixa, fatura de cartão, carteira de investimentos, saldos, empréstimos.

📁 [`skills/banco-mcp/SKILL.md`](skills/banco-mcp/SKILL.md)

Pra instalar, copie a pasta pra `~/.claude/skills/`:

```bash
cp -r skills/banco-mcp ~/.claude/skills/
```

Depois é só perguntar coisas como "Quanto gastei esse mês?" ou "Como está minha carteira de investimentos?" — a skill ativa automaticamente e orquestra as 13 tools.

---

## Preços

| Plano | Preço | Bancos | Perguntas |
|---|---|---|---|
| Free trial | R$ 0 | Ilimitadas | 10 / 24h |
| PF Solo | R$ 19,90/mês | 1 | Ilimitadas |
| PF Plus | R$ 29,90/mês | 3 | Ilimitadas |
| **PF Ilimitado** ⭐ | **R$ 49,90/mês** | **5 + R$9/extra** | Ilimitadas |
| PJ Ilimitado | R$ 99,90/mês | 4 + R$19,90/extra | Ilimitadas |

Detalhamento: [docs/precos.md](docs/precos.md). Cancela quando quiser, sem taxa.

---

## Privacidade & LGPD

- **Somente leitura** (regulado pelo Banco Central via Open Finance)
- **Consentimento explícito** por banco, revogável a qualquer momento
- **Retenção mínima**: dados apagados em 30 dias após desconectar
- **Sub-processadores**: agregador Open Finance autorizado pelo BACEN, instituições financeiras conectadas, LLM host que você escolher
- **Você no controle**: desconectar bancos, exportar dados, excluir conta

Política completa: [docs/privacidade-lgpd.md](docs/privacidade-lgpd.md)

> ⚠️ **Atenção**: os dados retornados pelas tools são enviados ao **LLM host que você escolher** (Anthropic / OpenAI / Cursor / agente próprio). Esse provedor é sub-processador fora do nosso controle. Recomendamos contratar planos com opt-out de treinamento ativado.

---

## Perguntas frequentes

**A IA pode mover dinheiro ou fazer transferência?**
Não. 100% somente leitura. Open Finance é regulado pelo Banco Central e não permite operações de movimentação por essa via. Nenhuma das 13 tools faz writes.

**Meus dados são usados pra treinar IA?**
Não pela gente. Os dados vão pro LLM host que você escolher (Claude, ChatGPT, etc.) — política de treinamento é responsabilidade do provedor de IA. Recomendamos opt-out.

**Quais bancos são suportados?**
Todos integrados ao Open Finance Brasil + bancos digitais via credenciais. Veja seção "Bancos suportados" acima.

**Como cancelo?**
`app.mcp.ai/banco → Settings → Assinatura → Cancelar`. Encerra no fim do ciclo, sem taxa.

**E se um banco não tiver Open Finance?**
A maioria dos bancos no Brasil já está no Open Finance. Alguns bancos digitais menores podem usar fallback via credenciais diretas — a conexão funciona mas pode ser menos estável.

**Posso usar com agente próprio (não Claude/Cursor)?**
Sim — qualquer cliente que suporte MCP over HTTP. Veja [INSTALL.md](INSTALL.md) seção "Outros clientes".

**O servidor é open source?**
O servidor é proprietário (hosted). Este repositório é o wrapper público com manifestos, docs e skills — tudo MIT. PRs e contribuições nas docs/skills são bem-vindas.

---

## Contribuindo

Issues, PRs e sugestões são bem-vindas. Especialmente:

- Traduções de docs/skills (EN ✓, ES, FR, IT)
- Novas skills do Claude Code
- Snippets de instalação pra clientes MCP novos
- Correções/melhorias na documentação

Por favor leia [SECURITY.md](SECURITY.md) antes de abrir issue com algo relacionado a segurança.

---

## Suporte

- 📧 [banco@mcp.ai](mailto:banco@mcp.ai) — dúvidas, sugestões, parcerias
- 🐛 [GitHub Issues](https://github.com/douglac/banco-mcp/issues) — bugs e features
- 📄 [docs/](docs/) — documentação completa em PT-BR

---

## Licença

MIT — veja [LICENSE](LICENSE).

O servidor MCP em `api.mcp.ai/banco` é proprietário (hosted). Este repositório (manifestos, docs, skills) é MIT.

Open Finance Brasil regulado pelo Banco Central.
