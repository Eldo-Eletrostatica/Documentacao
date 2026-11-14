# Testes de ponta a ponta

A suíte Selenium do repositório `Frontend` (pasta `e2e/`) abre o Chrome e usa o site como uma pessoa usaria: o visitante pede orçamento, o funcionário lança a venda, o gestor exporta o relatório. Ela roda por **fluxo de interação**, uma jornada que pode passar por várias pessoas.

A suíte completa roda no localhost ou na homologação, porque cria contatos, vendas e funcionários de teste. Na produção roda só o fluxo `producao`, que não grava nada ([ADR 0009](../adr/0009-homologacao-antes-da-producao.md)).

## Fluxos

| Fluxo | O que cobre | USs | Página |
| --- | --- | --- | --- |
| `vitrine` | Portfólio, detalhe do projeto, eventos e avaliações | US01, US02, US03, US05 | [Visitante](visitante.md#vitrine) |
| `contato` | Contato de pessoa física e orçamento pelo simulador e por PDF | US06, US07 | [Visitante](visitante.md#contato-e-orcamento) |
| `celular` | Site e área interna numa tela de 390 px | | [Visitante](visitante.md#celular) |
| `venda` | Do orçamento à venda, a OS, o status, o atraso e a consulta do cliente | US17 a US21 | [Equipe](equipe.md#venda) |
| `comissoes` | Regra de comissão, comissão do funcionário e relatório do gestor | US22, US23, US24 | [Equipe](equipe.md#comissoes) |
| `estoque` | Categoria, item, entrada e saída, alerta e histórico | US13 a US16 | [Equipe](equipe.md#estoque) |
| `gestao` | Dashboard e exportação do período | US11, US12 | [Equipe](equipe.md#gestao) |
| `administracao` | Login, funcionários, tabela de orçamento, identidade visual, portfólio e eventos | US08, US09, US10, US25, US26, US27 | [Administração](administracao.md) |
| `fumaca` | Um teste de cada fluxo, para conferir um deploy em poucos minutos | | [abaixo](#checagem-rapida-fumaca) |
| `producao` | Só leitura, para conferir a produção depois de uma promoção | | [Produção](producao.md) |

Cada página de fluxo traz os comandos para rodar só aquele fluxo e o vídeo de uma execução na homologação. Os vídeos foram gravados em 13/11/2026, com a pausa padrão de 1 s entre os passos; os trechos sem o navegador aberto (acordar o site, salvar e restaurar o banco) foram cortados.

## Antes de rodar

Precisa do Node 22, do Chrome e do Docker (que roda o cliente do PostgreSQL). Os comandos rodam na pasta do `Frontend`, depois do `npm install`. Para conferir se o Selenium abre o Chrome:

```bash
npm run e2e:check-driver
```

Antes de abrir o navegador, o script acorda o site e a API, que na homologação dormem depois de 15 minutos sem acesso, e tira uma cópia do banco. No fim, mesmo depois de uma falha ou de um Ctrl+C, carrega a cópia de volta: os dados de teste não ficam no banco.

## No localhost

Com o Backend de pé pelo `docker compose` dele (porta 3001) e o Frontend na porta 3000:

```bash
E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e
```

Sem argumento, rodam todos os fluxos. Com o nome dos fluxos, só eles: `npm run test:e2e -- venda estoque`. Se o Frontend usa outra porta, informe-a em `E2E_BASE_URL=http://localhost:<porta>`.

## Na homologação

Os valores da homologação ficam num arquivo **fora dos repositórios**, por exemplo `homolog-e2e.env`, ao lado das pastas `Frontend`, `Backend` e `Documentacao`. Nunca o commite.

```env
E2E_BASE_URL='https://eldo-site-homolog.onrender.com'
E2E_API_URL='https://eldo-api-homolog.onrender.com'
E2E_ADMIN_EMAIL='<admin da homologação>'
E2E_ADMIN_PASSWORD='<senha do admin da homologação>'
E2E_CAPTCHA_TOKEN='<E2E_CAPTCHA_TOKEN da eldo-api-homolog>'
E2E_DATABASE_URL='postgresql://<usuário>:<senha>@<host>/<banco>?sslmode=require'
```

- Use aspas simples em todos os valores: sem elas, um `#` na senha corta o valor.
- O `E2E_CAPTCHA_TOKEN` está no Render, em **eldo-api-homolog → Environment**.
- O `E2E_DATABASE_URL` vem do Neon, projeto `eldo-homolog`, em **Connect**, com o **Connection pooling** desligado.

```bash
node --env-file=../homolog-e2e.env e2e/run.js            # todos os fluxos
node --env-file=../homolog-e2e.env e2e/run.js venda      # só um fluxo
```

A primeira linha da saída mostra o destino. Se aparecer `localhost`, o arquivo não foi lido.

## Checagem rápida (fumaça)

O fluxo `fumaca` roda um teste de cada fluxo e leva poucos minutos:

=== "Localhost"

    ```bash
    E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e -- fumaca
    ```

=== "Homologação"

    ```bash
    node --env-file=../homolog-e2e.env e2e/run.js fumaca
    ```

O GitHub Actions roda esse fluxo sem tela depois de cada commit na `main`, assim que o Render publica o commit na homologação. Se falhar, a mudança não deve ser promovida para a `producao`. Outros fluxos rodam por **Actions → Selenium na homologação → Run workflow**. Onde essa etapa entra no caminho até a produção está em [Pipeline de deploy e testes](../deploy/pipeline.md).

## Ajustes da execução

| Variável | Efeito |
| --- | --- |
| `E2E_STEP_DELAY_MS` | Pausa entre os passos, para quem assiste. Padrão `1000`; `0` roda direto |
| `E2E_HEADLESS` | `true` roda sem abrir a janela do Chrome |

Um teste que falha não para os outros e salva um print em `e2e/screenshots/`. A lista completa de variáveis está no README do `Frontend`, em "Testes de ponta a ponta".
