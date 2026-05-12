# html-artifact

Uma skill do [Claude Code](https://claude.com/claude-code) que cria artefatos HTML ricos em arquivo único através de uma conversa curta de descoberta — e abre o resultado em um portal do [Maestri](https://www.themaestri.app/) ao lado do seu terminal.

Inspirada no artigo ["The Unreasonable Effectiveness of HTML"](https://x.com/trq212/status/2052809885763747935) do Thariq e no [repositório de exemplos](https://github.com/ThariqS/html-effectiveness).

## O que ela faz

Em vez de receber um pedido vago do tipo *"me faz um HTML"* e produzir algo genérico, a skill faz uma conversa curta — no máximo 2–3 perguntas — pra descobrir o que você realmente quer. Só então escreve um arquivo `.html` autocontido e abre num portal do Maestri (ou no navegador padrão se o Maestri não estiver disponível).

**A conversa é o produto. Gerar o HTML é a parte fácil.**

## Por que HTML em vez de Markdown?

- **Densidade de informação** — tabelas, layouts em CSS, diagramas SVG inline, interatividade embarcada.
- **Mais fácil de ler** — uma spec em markdown com 200 linhas é pulada. O mesmo conteúdo em HTML, com sumário lateral, é lido.
- **Mais fácil de compartilhar** — arquivo único, sem build, faz upload pro S3 ou manda o arquivo direto.
- **Mão dupla** — sliders, editores drag-drop e botões de "copiar como prompt" mantêm você no loop com o Claude.

## Instalação

Clone na pasta de skills do seu usuário:

```bash
git clone git@github.com:RodriguesCosta/html-artifact-skill.git ~/.claude/skills/html-artifact
```

Reinicie o Claude Code (ou recarregue as skills) e a skill fica disponível.

## Como usar

É só pedir pro Claude Code qualquer coisa que precise de um documento rico de uma vez só. A skill dispara automaticamente em pedidos como:

- "Me faz um arquivo HTML / um artefato HTML"
- "Cria um editor descartável pra X"
- "Compara esses 4 layouts lado a lado"
- "Escreve o plano de implementação em HTML"
- "Me mostra esse fluxo como diagrama SVG"
- "Gera um relatório de status da semana"

A skill faz algumas perguntas, recapitula o que entendeu em uma frase e escreve o arquivo em `./html-artifacts/<nome-descritivo>.html`. Depois abre num portal do Maestri ao lado do seu terminal.

### Categorias suportadas

A skill conhece o formato dessas categorias comuns de artefato:

- **Planos de implementação** com sumário lateral, mockups, diagramas de fluxo de dados, snippets de código
- **Diagramas SVG isolados** — flowcharts, sequência, grafos de dependência, mapas mentais
- **Explorações** — N variantes lado a lado pra escolher uma direção
- **Code reviews / PR writeups** com diffs anotados e achados categorizados por severidade
- **Explainers** — TL;DR, seções colapsáveis, snippets em abas, FAQ
- **Reports de status e incidentes** com timelines e gráficos
- **Slide decks** com navegação por setas
- **Editores descartáveis / protótipos** com botão de copiar/exportar bem visível
- **Referências de design system**

## Design system

Por padrão a skill usa a paleta Anthropic-style do [repositório html-effectiveness](https://github.com/ThariqS/html-effectiveness) — fundo ivory, acentos clay (laranja quente) e olive, headings em serif e corpo em sans-serif. Se você tem seu próprio design system, aponta o Claude pra ele durante a conversa e a skill segue suas tokens.

## Integração com Maestri

Quando rodando em um terminal do [Maestri](https://www.themaestri.app/), a skill abre o HTML gerado em um portal na canvas, ao lado do seu terminal — assim você itera sem sair da conversa.

- Primeira geração cria um portal novo.
- Em edições seguintes a skill pergunta uma vez por sessão se você prefere reusar o mesmo portal (sobrescreve no lugar) ou abrir um portal novo a cada iteração (sufixos `-v2`, `-v3` pra comparação lado a lado). Sua escolha vale pelo resto da sessão.

Se o `maestri` não estiver no PATH, a skill cai pra `open <arquivo>` no macOS.

## Onde os arquivos são salvos

Por padrão em `./html-artifacts/` (uma subpasta do diretório atual do terminal). A pasta é criada se não existir. Se você passar um caminho explícito, a skill respeita.

## Licença

MIT
