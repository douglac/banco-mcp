# Ferramentas

O Banco MCP expõe 13 ferramentas para o seu agente de IA. Todas são **somente leitura** — nenhuma move dinheiro, nenhuma altera dados no seu banco.

## Sumário

| # | Tool | O que faz |
|---|---|---|
| 1 | `openfinance_list_connections` | Lista bancos conectados |
| 2 | `openfinance_get_item_status` | Status de sincronização de uma conexão |
| 3 | `openfinance_list_accounts` | Contas correntes, poupanças e cartões de um banco |
| 4 | `openfinance_get_accounts_detail` | Detalhe completo de contas (limites, cartões adicionais) |
| 5 | `openfinance_get_account_balance` | Saldo atual em tempo real |
| 6 | `openfinance_list_transactions` | Extrato com filtro de período e keywords |
| 7 | `openfinance_list_credit_card_bills` | Faturas de cartão com `payment_status` derivado |
| 8 | `openfinance_get_credit_card_bill` | Detalhe de uma fatura específica |
| 9 | `openfinance_list_investments` | Carteira: FIIs, ações, renda fixa, fundos, previdência |
| 10 | `openfinance_list_investment_transactions` | Movimentações de um investimento |
| 11 | `openfinance_list_loans` | Contratos de empréstimo |
| 12 | `openfinance_list_categories` | Taxonomia de categorias de transação |
| 13 | `openfinance_force_sync` | Força sincronização imediata |
| 14 | `openfinance_disconnect_bank` | Remove conexão e revoga consentimento |

## Detalhe de cada tool

### 1. `openfinance_list_connections`
**Op**: read · **Schema**: nenhum input
Lista todos os bancos que você já conectou (id, nome, status). Retorna também um link pra adicionar mais bancos.

### 2. `openfinance_get_item_status`
**Op**: read · **Input**: `item` (item_id, connector_id ou connector_name)
Status atual: `UPDATED`, `UPDATING`, `LOGIN_ERROR`, etc. Use pra diagnosticar quando uma conexão parou de funcionar.

### 3. `openfinance_list_accounts`
**Op**: read · **Input**: `item` (opcional), `type` (`BANK` | `CREDIT`)
Lista contas (corrente, poupança) e cartões de um banco. Retorna `bank` (marca, ex. "Nubank Empresas") e `connector_id`.

### 4. `openfinance_list_transactions`
**Op**: read · **Input**: `account_id` (obrigatório), `from`, `to`, `page`, `page_size`, `search_queries`
Extrato. Filtra por data e palavras-chave (ex. `["uber", "ifood"]`). Pra cartão de crédito, **é a única forma** de pegar transações itemizadas — cada lançamento traz `creditCardMetadata.billId` ligando à fatura.

### 5. `openfinance_list_credit_card_bills`
**Op**: read · **Input**: `account_id` (obrigatório, tipo `CREDIT`)
Faturas de um cartão. **Atenção**: Open Finance Brasil não retorna campo `paid` na fatura — esta tool deriva um `payment_status` (`PAID`, `OPEN`, `PAST_DUE_UNCONFIRMED`, `PAST_DUE_UNPAID`) cruzando pagamentos entre faturas (regra de ciclo: pagamento entre fechamento e vencimento entra na fatura SEGUINTE).

### 6. `openfinance_list_investments`
**Op**: read · **Input**: `item` (opcional), `type` (`EQUITY`, `ETF`, `FIXED_INCOME`, `MUTUAL_FUND`, `SECURITY`, `COE`, `OTHER`)
Carteira de investimentos. Cada item traz balance, amount, profitabilidade (lastMonthRate, annualRate, etc.).

### 7. `openfinance_list_investment_transactions`
**Op**: read · **Input**: `investment_id` (obrigatório)
Histórico de compras, vendas, juros, impostos de um investimento. Inclui `expenses` itemizado (brokerageFee, incomeTax, custodyFee).

### 8. `openfinance_list_loans`
**Op**: read · **Input**: `items` (array de seletores de conexão)
Contratos de empréstimo (crédito pessoal, consignado, etc.) por conexão bancária.

### 9. `openfinance_force_sync`
**Op**: write (não move dinheiro — só pede sync ao agregador) · **Input**: `items` (array, 1–50)
Força sincronização imediata. Use quando os dados parecem desatualizados.

### 10. `openfinance_get_account_balance`
**Op**: read · **Input**: `account_ids` (array uuid, 1–50)
Saldo em tempo real. Cartão de crédito pode retornar `warning` se o banco não expõe saldo real-time.

### 11. `openfinance_get_accounts_detail`
**Op**: read · **Input**: `account_ids` (array uuid, 1–50)
Objeto completo da conta, incluindo `creditData` extendido (limites totais, limites disponíveis, cartões adicionais).

### 12. `openfinance_get_credit_card_bill`
**Op**: read · **Input**: `bill_ids` (array uuid, 1–50)
Detalhe de fatura. **Não retorna transações** — pra isso use `openfinance_list_transactions` com o `account_id` do cartão.

### 13. `openfinance_list_categories`
**Op**: read · **Schema**: nenhum input
Taxonomia de categorias (Alimentação, Transporte, Lazer, etc.) usada nas transações.

### 14. `openfinance_disconnect_bank`
**Op**: delete · **Input**: `item` (item_id, connector_id ou connector_name)
Revoga o consentimento Open Finance daquele banco e apaga a conexão. Pode reconectar depois.

## Prompts de exemplo

```
Liste meus bancos conectados
Quanto gastei esse mês com restaurantes e mercado?
Liste minhas últimas 20 transações em todas as contas
Resuma minhas faturas de cartão em aberto
Como está minha carteira de investimentos hoje?
Quais foram meus maiores gastos no último trimestre?
Compara minha fatura desse mês com a do mês passado
Quanto tenho em renda fixa vs ações?
```

## Limites e cuidados

- **Rate limit**: o servidor é generoso, mas se você fizer muitas chamadas em loop, pode receber `429`. Espere alguns segundos e tente de novo.
- **Pagina_size máx**: 500 (transações) ou 100 (faturas).
- **Force_sync demora**: o agregador precisa autenticar de novo no banco. Pode levar 30s–2min.
- **Cartão sem balance real-time**: alguns bancos não expõem. A tool retorna `warning` em vez de falhar.
