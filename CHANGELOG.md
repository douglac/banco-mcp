# Changelog

Todas as mudanças relevantes do Banco MCP ficam aqui. Segue [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/) e [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0] — 2026-05-12

### Adicionado
- Lançamento público do repositório
- 13 ferramentas Open Finance:
  - `openfinance_list_connections`, `openfinance_get_item_status`
  - `openfinance_list_accounts`, `openfinance_get_accounts_detail`, `openfinance_get_account_balance`
  - `openfinance_list_transactions`
  - `openfinance_list_credit_card_bills`, `openfinance_get_credit_card_bill`
  - `openfinance_list_investments`, `openfinance_list_investment_transactions`
  - `openfinance_list_loans`
  - `openfinance_list_categories`
  - `openfinance_force_sync`, `openfinance_disconnect_bank`
- Manifestos pra Claude Desktop, Cursor e VS Code (configuração única — auth resolvida em runtime via OAuth 2.1 ou tool `authenticate` para clientes sem OAuth)
- Uma Claude Code skill geral (`banco-mcp`) que cobre todos os casos de uso financeiros — análise de gastos, extrato mensal, fluxo de caixa, fatura, investimentos, saldos, empréstimos — em um arquivo único
- Documentação PT-BR: instalação, ferramentas, autenticação, privacidade/LGPD, preços
- README em PT-BR e EN
