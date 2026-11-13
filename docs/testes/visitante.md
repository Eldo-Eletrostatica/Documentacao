# Visitante

Fluxos de quem chega ao site público, sem login. A preparação e as variáveis estão em [Como rodar](index.md).

## Vitrine

O visitante conhece o trabalho da Eldo antes de pedir orçamento. Arquivo `01-visitante-vitrine`.

| Teste | US |
| --- | --- |
| Vê o portfólio e filtra por categoria | US01 |
| Abre o detalhe de um projeto | US02 |
| Vê os eventos e abre um registro | US03 |
| Vê as avaliações do Google ou o aviso de indisponível | US05 |

### Comandos para rodar

=== "Localhost"

    ```bash
    E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e -- vitrine
    ```

=== "Homologação"

    ```bash
    node --env-file=../homolog-e2e.env e2e/run.js vitrine
    ```

### Vídeo da execução

<video controls preload="metadata" width="100%" src="../../assets/videos/vitrine.mp4"></video>

## Contato e orçamento

A pessoa física manda um contato para o atendente; a pessoa jurídica pede orçamento pelo simulador ou enviando o projeto em PDF. Arquivo `02-visitante-contato`.

| Teste | US |
| --- | --- |
| Pessoa física envia o formulário de contato | US06 |
| Pessoa jurídica pede o orçamento pelo simulador | US07 |
| Pessoa jurídica envia o projeto em PDF | US07 |

### Comandos para rodar

=== "Localhost"

    ```bash
    E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e -- contato
    ```

=== "Homologação"

    ```bash
    node --env-file=../homolog-e2e.env e2e/run.js contato
    ```

### Vídeo da execução

<video controls preload="metadata" width="100%" src="../../assets/videos/contato.mp4"></video>

## Celular

O site público e a área interna numa tela de 390 px: nada passa da largura da tela, e o menu leva a cada página. Arquivo `09-celular`.

| Teste |
| --- |
| O site público cabe na tela do celular e o menu navega |
| A área interna cabe na tela do celular e o menu navega |

### Comandos para rodar

=== "Localhost"

    ```bash
    E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e -- celular
    ```

=== "Homologação"

    ```bash
    node --env-file=../homolog-e2e.env e2e/run.js celular
    ```

### Vídeo da execução

<video controls preload="metadata" width="360" src="../../assets/videos/celular.mp4"></video>
