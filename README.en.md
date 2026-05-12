# Banco MCP

### Brazilian Open Finance for Claude, Cursor and AI agents

Connect **Itaú, Bradesco, Santander, Nubank, Inter, Banco do Brasil, Caixa, C6** and 25+ other Brazilian banks to your AI agent. Ask about your spending, statements, investments and credit cards in **natural language**. Open Finance regulated by Brazil's Central Bank, in 30 seconds.

[![Banco MCP — Demo](https://img.youtube.com/vi/cuOEJYbSNnU/maxresdefault.jpg)](https://www.youtube.com/watch?v=cuOEJYbSNnU "▶ Watch on YouTube")

- 🇧🇷 **30+ Brazilian banks** supported (the first Open Finance MCP for Brazil)
- 💬 **Works with any MCP client**: Claude Desktop, Cursor, VS Code, Cline, Continue
- 🔒 **Read-only** — the AI cannot move money
- 📊 **13 tools**: statements, balances, bills, investments, loans
- 🤖 **Claude Code skill** included (see `skills/banco-mcp/`)
- ⚡ **Open Finance regulated** by Brazil's Central Bank
- 🛡️ **LGPD-compliant** — explicit consent, minimal retention

[Portuguese version](README.md) · [Full docs (PT-BR)](docs/)

---

## One-click install

### Claude (Web and Desktop)

Anthropic unified MCP installation at `claude.ai/customize/connectors`. **The same link works for both Claude Web and Claude Desktop** (you just need to be logged into your account):

[➕ Open in Claude and connect](https://claude.ai/customize/connectors?modal=add-custom-connector&mcpName=Banco%20MCP&mcpServerUrl=https%3A%2F%2Fapi.mcp.ai%2Fbanco)

This link opens the "Add custom connector" modal with **name** (`Banco MCP`) and **URL** (`https://api.mcp.ai/banco`) pre-filled. Click **Add**, log in via magic-link on the first call, connect your banks.

**Manual** (if the deeplink doesn't open): go to [claude.ai/customize/connectors](https://claude.ai/customize/connectors) → click **+** → **Add custom connector** → paste:
- **Name**: `Banco MCP`
- **URL**: `https://api.mcp.ai/banco`

### Cursor

[➕ Install Banco MCP in Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=banco&config=eyJ1cmwiOiJodHRwczovL2FwaS5tY3AuYWkvYmFuY28ifQ==)

### VS Code (Copilot Chat)

[➕ Install Banco MCP in VS Code](vscode:mcp/install?name=banco&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fapi.mcp.ai%2Fbanco%22%7D)

### ChatGPT, Manus, OpenClaw and 40+ other clients

Banco MCP works in any MCP client that supports **MCP over HTTP**. The server URL is always:

```
https://api.mcp.ai/banco
```

See [INSTALL.md](INSTALL.md#outros-clientes-mcp) for per-client config examples, or [banco.mcp.ai](https://banco.mcp.ai) for visual instructions.

**Detailed instructions**: [INSTALL.md](INSTALL.md) — manual file config (`claude_desktop_config.json`, `.cursor/mcp.json`, `.vscode/mcp.json`), `agent-auth` flow for clients without OAuth, and troubleshooting.

---

## Ask about your money

After connecting at least one bank, ask things like:

```
How much did I spend this month on restaurants and groceries?
List my last 20 transactions across all accounts
Summarize my open credit card bills
How is my investment portfolio doing today?
What were my biggest expenses last quarter?
Compare this month's bill with last month's
How much do I have in fixed income vs stocks?
Detect recurring charges in the last 6 months
```

---

## 13 available tools

| Tool | Description |
|---|---|
| `openfinance_list_connections` | List connected banks |
| `openfinance_get_item_status` | Sync status of a connection |
| `openfinance_list_accounts` | Checking, savings and credit card accounts |
| `openfinance_get_accounts_detail` | Full detail (limits, additional cards) |
| `openfinance_get_account_balance` | Real-time balance |
| `openfinance_list_transactions` | Statements with date and keyword filters |
| `openfinance_list_credit_card_bills` | Credit card bills with derived `payment_status` |
| `openfinance_get_credit_card_bill` | Specific bill detail |
| `openfinance_list_investments` | Portfolio: REITs, stocks, fixed income, funds, pension |
| `openfinance_list_investment_transactions` | Movements of a specific investment |
| `openfinance_list_loans` | Loan contracts per connection |
| `openfinance_list_categories` | Transaction category taxonomy |
| `openfinance_force_sync` | Force immediate sync |
| `openfinance_disconnect_bank` | Remove connection and revoke consent |

---

## How it works

```
1. Install the MCP in your client (Claude/Cursor/VS Code)
2. On first query, browser opens for magic-link
3. Create account (email only, no password) and connect banks via Open Finance
4. Grant Open Finance consent (read-only, revocable)
5. Done — ask in natural language
```

---

## Pricing

| Plan | Price | Banks | Queries |
|---|---|---|---|
| Free trial | R$ 0 | Unlimited | 10 / 24h |
| Personal Solo | R$ 19.90/mo | 1 | Unlimited |
| Personal Plus | R$ 29.90/mo | 3 | Unlimited |
| **Personal Unlimited** ⭐ | **R$ 49.90/mo** | **5 + R$9/extra** | Unlimited |
| Business Unlimited | R$ 99.90/mo | 4 + R$19.90/extra | Unlimited |

Cancel anytime, no fee. Pricing in BRL (Brazilian Real).

---

## Privacy & LGPD

- **Read-only** (regulated by Brazil's Central Bank via Open Finance)
- **Explicit per-bank consent**, revocable anytime
- **Minimum retention**: data deleted 30 days after disconnect
- **Sub-processors**: Brazilian Open Finance aggregator authorized by the Central Bank, connected financial institutions, LLM host you choose
- **You're in control**: disconnect banks, export data, delete account

Full policy: [docs/privacidade-lgpd.md](docs/privacidade-lgpd.md) (PT-BR)

> ⚠️ **Note**: data returned by tools is forwarded to the **LLM host you choose** (Anthropic / OpenAI / Cursor / your own agent). That provider is a sub-processor outside our control. We recommend subscribing with training opt-out enabled.

---

## FAQ

**Can the AI move money or make transfers?**
No. 100% read-only. Open Finance is regulated by Brazil's Central Bank and does not allow money movement via this protocol. None of the 13 tools do writes.

**Is my data used to train AI?**
Not by us. Data goes to the LLM host you choose (Claude, ChatGPT, etc.) — training policy is on that provider. We recommend opt-out.

**Which banks are supported?**
All banks integrated with Brazil's Open Finance plus digital banks via credentials.

**Can I use it with my own agent (not Claude/Cursor)?**
Yes — any client supporting MCP over HTTP. See [INSTALL.md](INSTALL.md) "Other clients".

**Is the server open source?**
The server is proprietary (hosted). This repo is the public wrapper with manifests, docs and skills — all MIT.

---

## Contributing

Issues, PRs and suggestions welcome. Especially:

- Doc/skill translations (PT ✓, EN ✓, ES, FR, IT)
- New Claude Code skills
- Install snippets for new MCP clients
- Documentation fixes/improvements

Please read [SECURITY.md](SECURITY.md) before opening security-related issues.

---

## Support

- 📧 [banco@mcp.ai](mailto:banco@mcp.ai) — questions, suggestions, partnerships
- 🐛 [GitHub Issues](https://github.com/douglac/banco-mcp/issues) — bugs and features
- 📄 [docs/](docs/) — full documentation (PT-BR)

---

## License

MIT — see [LICENSE](LICENSE).

The MCP server at `api.mcp.ai/banco` is proprietary (hosted). This repository (manifests, docs, skills) is MIT.

Brazilian Open Finance regulated by the Central Bank.
