---
name: banco-mcp
description: Skill abrangente pra trabalhar com dados financeiros via Banco MCP — analisar gastos, gerar extratos, fluxo de caixa, fatura de cartão, carteira de investimentos. Use sempre que o usuário perguntar sobre dinheiro, gastos, banco, cartão, investimentos, fatura, saldo, extrato, transações, ou variantes. Orquestra as 13 ferramentas openfinance_* (read-only) do servidor MCP remoto em https://api.mcp.ai/banco.
---

# Banco MCP — skill geral

Skill única que cobre todos os casos de uso do Banco MCP. Use sempre que o usuário fizer perguntas financeiras sobre as próprias contas.

## Quando ativar

Qualquer pergunta sobre dinheiro pessoal/empresarial do usuário:

- **Gastos / análise**: "Quanto gastei?", "no que estou gastando?", "meus maiores gastos", "gastos por categoria", "compara com mês passado"
- **Extrato / movimentação**: "extrato do mês", "fechamento", "como foi minha movimentação", "quanto entrou e quanto saiu"
- **Fluxo de caixa**: "fluxo de caixa", "evolução dos últimos meses", "tendência", "comparativo mensal"
- **Cartão de crédito**: "minha fatura", "quanto vou pagar de cartão", "parcelados", "recorrências"
- **Investimentos**: "minha carteira", "patrimônio investido", "rentabilidade", "quanto tenho em renda fixa"
- **Saldos / contas**: "saldo atual", "quantos bancos tenho conectados", "saldo do Nubank"
- **Empréstimos**: "meus financiamentos", "consignado", "crédito pessoal"

## Setup inicial (uma vez por sessão)

Antes de qualquer análise, descubra o cenário do usuário:

1. **`openfinance_list_connections`** → bancos conectados.
   - Se 0 → instrua "Conecte um banco em https://app.mcp.ai/banco antes de continuar."
   - Senão, guarde os `item_id` e `connector_name`.
2. **`openfinance_list_accounts`** por conexão → todas as contas + cartões. Cacheie `account_id`, `type` (BANK/CREDIT), `bank`.

Só faça essas duas chamadas uma vez por conversa. Reaproveite o cache.

## Casos de uso — playbooks

### 1. Análise de gastos

**Plano**:
1. Identificar período (padrão: mês atual). Converter pra `from`/`to` ISO `YYYY-MM-DD`.
2. Pra cada conta `type: BANK`, `openfinance_list_transactions(account_id, from, to, page_size: 500)`. Pagine.
3. Agregar:
   - **Total gasto** (sum onde `amount < 0`)
   - **Total recebido** (sum onde `amount > 0`)
   - **Saldo líquido** = recebido − |gasto|
   - **Top 10 categorias** (group by `category`)
   - **Top 10 merchants** (group by `merchant.name`)
   - **Recorrências**: merchants que aparecem ≥ 3× com variação de valor ≤ 5%
4. **Comparar com período anterior** se relevante.
5. Apresentar em tabela markdown. R$ com 2 casas. Categorize merchants conhecidos (Uber/99 → Transporte; iFood/Rappi → Alimentação) se `category` vier vazia.

### 2. Extrato mensal consolidado

**Plano**:
1. Identificar mês. `from` = dia 1, `to` = último dia.
2. `openfinance_list_accounts` (`type: BANK`).
3. `openfinance_get_account_balance(account_ids: [...])` → saldos atuais.
4. `openfinance_list_transactions` por conta no período.
5. Agregar por conta E consolidado:
   - Saldo final = saldo atual (snapshot agora)
   - Saldo inicial = saldo final − soma das transações
   - Entradas = sum > 0; Saídas = sum < 0
6. Tabela: `Conta | Saldo inicial | Entradas | Saídas | Saldo final` + linha **Total**.
7. Resumo executivo de 1–2 linhas com destaque do mês.

### 3. Fluxo de caixa rolling

**Plano**:
1. Janela padrão: 6 meses (aceite 3 / 12 / N meses).
2. Pra cada mês na janela, pra cada conta `type: BANK`: `openfinance_list_transactions(from, to)`.
3. Agregar por mês: entradas, saídas, líquido.
4. Tendência: regressão linear no líquido. > 5% inclinação = "subindo"; < −5% = "caindo"; entre = "estável".
5. Tabela `Mês | Entradas | Saídas | Líquido` + tendência abaixo.
6. Insights opcionais: "Entradas estáveis em ~R$ X sugere salário fixo".

### 4. Cartão de crédito

**Plano**:
1. `openfinance_list_accounts(type: CREDIT)` → cartões.
2. Pra cada, `openfinance_list_credit_card_bills(account_id)`.
3. Identificar fatura atual (`payment_status: OPEN`).
4. **Itemizar transações da fatura**: `openfinance_list_transactions(account_id do cartão, from = dueDate−30d, to = dueDate)`. Pra cartão, este é o ÚNICO jeito de ver lançamentos itemizados.
5. Análise:
   - Top categorias, top merchants
   - **Parcelados ativos**: filtrar `creditCardMetadata.installments.total > 1` — mostrar quantos meses restam
   - **Recorrências**: merchants em faturas anteriores com valor similar
6. Comparação com fatura anterior.

**Atenção a `payment_status`**:
- `PAID` — confiável, fatura paga
- `OPEN` — atual, em aberto
- `PAST_DUE_UNCONFIRMED` — **NÃO chame de "vencida" categoricamente**. Pode ter sido paga entre fechamento e vencimento e ainda não refletido. Sempre mencione: "Status indica que pode ter sido pago — só confirma quando a próxima fatura fechar."
- `PAST_DUE_UNPAID` — confirmado não pago

### 5. Carteira de investimentos

**Plano**:
1. `openfinance_list_investments()` por conexão (sem filtro de `type` na primeira chamada).
2. Agregar por `type`:
   - `EQUITY` (ações), `ETF`, `FIXED_INCOME` (CDB/LCI/LCA/Tesouro), `MUTUAL_FUND` (fundos), `SECURITY` (previdência), `COE`, `OTHER`
3. Pra cada tipo: total investido (`amount`), valor atual (`balance`), `amountProfit`, rentabilidade % (`annualRate` ou `lastTwelveMonthsRate` quando disponível).
4. Tabela: `Tipo | Valor atual | % carteira | Rentabilidade 12m`.
5. Detalhe por ativo opcional (top 10 posições).
6. Movimentações específicas: `openfinance_list_investment_transactions(investment_id)` com BUY/SELL/TAX/INTEREST/AMORTIZATION.

### 6. Saldos / status rápido

- "Quantos bancos?" → `openfinance_list_connections` (use o número e os nomes).
- "Saldo atual" → `openfinance_get_account_balance(account_ids: [...])`. Cartão pode retornar warning.
- "Status da conexão Itaú" → `openfinance_get_item_status(item: "Itaú")`.

### 7. Empréstimos

- `openfinance_list_loans(items: [...todas as conexões])` → contratos por banco.

## Regras transversais

### Formatação
- Valores sempre em **R$ com 2 casas decimais** (`R$ 1.234,56`).
- Datas em **DD/MM/YYYY** ou nome do mês.
- Use **tabela markdown** pra qualquer agregação. Bullets pra resumo.

### Cuidados
- **Não invente valores**. Se uma tool retornar `warning` ou `error`, comunique ao usuário.
- **Não chame `openfinance_force_sync` automaticamente** — caro e demora. Só sugira se dados parecem desatualizados (comparar `updated_at` da conexão com agora).
- **Cartão ≠ conta**: cartão tem saldo negativo (= o que você deve). Não some com conta corrente.
- **Pagamento de cartão é saída**, mas é movimentação interna — mencione separado se relevante.
- **Conta nova** (criada no meio do período): saldo inicial = saldo na abertura. Indique.
- **PJ vs PF**: pergunte se o usuário tem o plano PJ Ilimitado, pra separar análises.
- **Privacidade no chat**: não exiba nome do banco junto com saldo se o usuário pode estar compartilhando tela. Pergunte se quer mascarar.

### Não faça
- ❌ Recomendar comprar/vender ativos. Você é assistente, não corretor.
- ❌ Aconselhar parcelar/quitar dívida. Apresente os fatos, deixe o usuário decidir.
- ❌ Comparar com benchmarks ("seu ROI tá abaixo do CDI") sem o usuário pedir.
- ❌ Avisar sobre IR sem aviso de que isso é responsabilidade do contador.
- ❌ Mostrar valores em centavos sem formatar.

### Erros comuns
- ❌ Somar `amount` (investido) com `balance` (atual) — são coisas diferentes.
- ❌ Esquecer de filtrar por `from`/`to` (puxa histórico inteiro).
- ❌ Confundir pagamentos em `payments[]` da fatura — em Open Finance Brasil, eles refletem o ciclo da fatura ATUAL, mas tipicamente são o pagamento da fatura ANTERIOR. Use o `payment_status` derivado.
- ❌ Somar transações de cartão e conta corrente como "gastos" sem separar.
- ❌ Considerar saldo (snapshot) como entrada/saída.

## Output esperado (template universal)

```markdown
## [Título da análise] — [Período]

**Resumo**: [1-2 linhas com a conclusão principal]

### [Seção 1]
[Tabela ou bullets]

### [Seção 2]
[Tabela ou bullets]

### Insights
- [Observação relevante 1]
- [Observação relevante 2]
```

Mantenha output enxuto — quando for muita info, ofereça "Quer detalhar X?" em vez de jogar tudo de uma vez.
