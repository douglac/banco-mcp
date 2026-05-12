# Assets

Recursos visuais do projeto.

## Arquivos atuais

- `logo.svg` — logotipo placeholder (banco minimalista, roxo `#7C3AED` que é o brand color do botão "Conectar banco" no seed). **Substituir** pelo logo definitivo antes do launch público nas redes / Smithery / etc.

## Pendente (adicionar antes do launch)

- `logo.png` — versão raster 512×512 (alguns registries exigem PNG)
- `logo-dark.svg` — versão pra fundo escuro
- `favicon.ico` (se usar como site)
- `screenshots/` — 4–6 capturas:
  - `claude-desktop-conectado.png` — Claude Desktop mostrando "banco" como MCP ativo
  - `pergunta-gastos.png` — exemplo de prompt + resposta com dados sintéticos
  - `fatura-cartao.png` — análise de fatura
  - `investimentos.png` — carteira consolidada
  - `app-mcp-ai-banco.png` — UI de conexão de banco
- `demo.gif` — animação 10–15s mostrando o fluxo: instalar → conectar banco → primeira pergunta

## Regras pra screenshots

> ⚠️ **CRÍTICO**: nunca commitar screenshots com **dados reais**. Use:
> - Ambiente sandbox do agregador (consulte instruções internas pro fluxo de teste)
> - Dados sintéticos (saldos R$ 1.234, transações fictícias)
> - Ou borre/anonimize qualquer info pessoal

Imagens raw com dados reais devem ficar em `assets/screenshots-raw/` (gitignored).

## Pra registries (Smithery, PulseMCP, Glama)

A maioria pede:
- Logo quadrado (mínimo 512×512, idealmente 1024×1024)
- 2–4 screenshots de uso
- Demo gif/vídeo (opcional)

Otimize tamanho: SVG é o ideal pra logo; PNGs comprimidos via `pngquant` ou `tinypng` pra screenshots; GIFs via `gifsicle -O3` ou converta pra MP4 quando o registry aceita.
