# Privacidade & LGPD

Banco MCP é um serviço de leitura de dados financeiros via Open Finance Brasil — regulado pelo Banco Central. Esta página detalha como tratamos seus dados, baseada na LGPD (Lei Geral de Proteção de Dados Pessoais, Lei nº 13.709/2018).

## Princípios

1. **Read-only**: nenhuma ferramenta movimenta dinheiro, faz transferências ou altera dados no seu banco.
2. **Consentimento explícito**: você autoriza expressamente cada banco, e pode revogar a qualquer momento.
3. **Mínimo necessário**: só dados financeiros (saldos, transações, faturas, investimentos, empréstimos). Não capturamos dados sensíveis fora desse escopo.
4. **Você no controle**: pode desconectar bancos, gerar/revogar tokens, excluir sua conta a qualquer momento.

## Dados coletados

Quando você conecta um banco via Open Finance, agregamos:

- **Financial · Account**: dados de conta corrente/poupança (id, número, agência, tipo, subtipo, marca do banco, saldo)
- **Financial · Transaction**: lançamentos (data, descrição, valor, categoria, merchant)
- **Financial · Credit Card Bill**: faturas (vencimento, valor total, pagamento mínimo, pagamentos registrados)
- **Financial · Investment**: posições e movimentações de carteira (FIIs, ações, renda fixa, fundos, previdência)
- **Personal · Holder Name**: nome do titular da conta (retornado pelo Open Finance)

**NÃO coletamos**: CPF, RG, senha de banco, biometria, geolocalização, dados de saúde, contatos do telefone.

## Como autoriza

1. Você cria conta em `app.mcp.ai/banco` (e-mail + magic-link, sem senha)
2. Clica em **Conectar banco** e seleciona um banco
3. É redirecionado pro fluxo do banco (autenticação Open Finance, regulada pelo Banco Central)
4. Concede consentimento **explícito e revogável** com texto:

> "Autorizo a leitura dos meus dados bancários e de investimentos (saldos, transações, faturas, posições de carteira, rentabilidade e movimentações) via Open Finance Brasil, aceito os termos de uso do agregador autorizado pelo Banco Central, e entendo que os dados serão enviados ao assistente de IA que eu escolher (Claude, ChatGPT, Cursor, etc.), cuja política de privacidade é de minha responsabilidade conhecer."
>
> *(O texto exato do consentimento, incluindo o nome do agregador autorizado, é exibido na tela de conexão no fluxo `/connect`.)*

5. Voltamos com o consentimento OK → seu banco está conectado

## Como revoga

- **Um banco**: `app.mcp.ai/banco → seleciona o banco → Desconectar`. Revoga o consentimento Open Finance e apaga a conexão.
- **Todos**: `app.mcp.ai/banco → Settings → Desconectar todos`.
- **A conta inteira**: `Settings → Excluir conta`. Vide "Retenção" abaixo.

## Sub-processadores

Pra entregar o serviço, dados passam por:

| Sub-processador | País | Papel |
|---|---|---|
| **Agregador Open Finance autorizado pelo Banco Central do Brasil** | BR | Agregador regulado (nome exato divulgado no fluxo de conexão) |
| **Provedor de cloud contratado pelo agregador** | BR | Hospedagem |
| **Instituições financeiras** (Itaú, Nubank, etc.) | BR | Fonte de dados (você é cliente delas) |
| **LLM host escolhido por você** (Claude, ChatGPT, Cursor, agente próprio) | varia | Consumidor dos dados |

> **Atenção**: o LLM host (Anthropic/OpenAI/Cursor/seu próprio) é um sub-processador **fora do nosso controle**. Os dados retornados pelas tools são enviados pra ele a cada chamada. A política de privacidade do provedor de IA que você escolher se aplica — recomendamos contratar planos com **opt-out de treinamento** ativado.

## Retenção

- **Dados financeiros (transações, saldos, faturas)**: cacheados pelo período da assinatura ativa no agregador. Não armazenamos histórico além disso — perguntas históricas são respondidas via Open Finance em tempo real (ou cache curto).
- **Após desconectar um banco**: dados daquela conexão são apagados em **30 dias** (janela técnica de revogação).
- **Após excluir a conta**: dados pessoais (e-mail, tokens, conexões) apagados em **30 dias** salvo obrigação legal (ex.: fraude reportada). Logs anônimos podem ser retidos por mais tempo pra fins de segurança e auditoria.

## Categorias de dados (taxonomia)

- `financial.account`
- `financial.transaction`
- `financial.credit_card_bill`
- `financial.investment`
- `personal.holder_name`

## Seus direitos (LGPD)

Como titular dos dados, você tem direito a:

- **Confirmar** que tratamos seus dados
- **Acessar** seus dados (exportável em JSON via `app.mcp.ai/banco → Settings → Exportar dados`)
- **Corrigir** dados incorretos (entre em contato — nossos dados vêm do Open Finance, mas registramos correções)
- **Anonimizar, bloquear ou eliminar** dados desnecessários (excluir conta)
- **Portar** dados a outro fornecedor
- **Saber com quem compartilhamos** (lista acima)
- **Revogar consentimento** a qualquer momento (Desconectar banco)
- **Opor-se** a tratamento

Para exercer qualquer direito: [banco@mcp.ai](mailto:banco@mcp.ai). Respondemos em até 15 dias úteis (LGPD Art. 19).

## Encarregado de Proteção de Dados (DPO)

Atualmente o ponto de contato é [banco@mcp.ai](mailto:banco@mcp.ai). DPO formal será nomeado quando a base de usuários ativos justificar (em consonância com Art. 41 da LGPD e regulamentação ANPD).

## Base legal

- **Consentimento** (Art. 7º, I) — você autoriza expressamente cada banco
- **Execução de contrato** (Art. 7º, V) — pra entregar o serviço você contratou
- **Cumprimento de obrigação legal** (Art. 7º, II) — Open Finance regulado pelo BACEN

## Termos do agregador Open Finance

O Banco MCP usa um agregador autorizado pelo Banco Central do Brasil pra acessar Open Finance. Ao conectar, você também aceita os termos de uso do agregador — os links exatos pros Termos de Uso e Política de Privacidade do agregador são exibidos diretamente na tela de conexão (`/connect`) antes do click-wrap.

## Transferência internacional

Dados são armazenados no Brasil. Mas quando você usa o MCP com um LLM host hospedado no exterior (ex.: Anthropic nos EUA), os dados retornados pelas tools são transmitidos pra esse host fora do Brasil. Esse fluxo é responsabilidade sua e governado pela política do provedor de IA que você escolheu.

## Mudanças nesta política

Quando atualizamos, registramos no `CHANGELOG.md` do repositório e notificamos por e-mail os usuários ativos. Mudanças materiais ficam com janela de 30 dias antes de entrarem em vigor.

## Reclamações

Se você acha que tratamos seus dados de forma inadequada:

1. Tente resolver direto: [banco@mcp.ai](mailto:banco@mcp.ai)
2. Reclame na ANPD: [www.gov.br/anpd](https://www.gov.br/anpd/pt-br)

**Última atualização**: 2026-05-12.
