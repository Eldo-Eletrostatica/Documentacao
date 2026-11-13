# Equipe

Fluxos da área interna, que passam pelo funcionário comercial, pelo funcionário de estoque e pelo gestor. A suíte cria o gestor e o funcionário de teste (`e2e.gestor@eldo.test` e `e2e.funcionario@eldo.test`) quando eles não existem. A preparação e as variáveis estão em [Como rodar](index.md).

## Venda

Do orçamento pedido no site até o cliente acompanhar a OS: o gestor envia o valor e lança a venda, a OS nasce da venda, muda de status e aparece no painel de atrasos, e o cliente a consulta pelo protocolo ou pelo CNPJ. Arquivos `07-vendas-comissoes` (só os testes da US21), `06-servicos` e `03-cliente-consulta-os`.

| Teste | US |
| --- | --- |
| O gestor envia o valor do orçamento do site, registra a aprovação e lança a venda | US21 |
| O funcionário lança uma venda direta para uma empresa nova | US21 |
| A OS nasce da venda e aparece no kanban e na lista | US17 |
| O funcionário atualiza o status da OS até concluir | US18 |
| O gestor vê o serviço atrasado no painel de atrasos | US20 |
| O cliente consulta a OS pelo protocolo e as OS em andamento pelo CNPJ | US19 |
| Um protocolo inexistente mostra o aviso | US19 |

### Comandos para rodar

=== "Localhost"

    ```bash
    E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e -- venda
    ```

=== "Homologação"

    ```bash
    node --env-file=../homolog-e2e.env e2e/run.js venda
    ```

### Vídeo da execução

<video controls preload="metadata" width="100%" src="../../assets/videos/venda.mp4"></video>

## Comissões

O administrador define a regra de comissão, o funcionário vê quanto acumulou e o gestor confere o relatório e o ranking. Arquivos `07-vendas-comissoes` (US22 e US23) e `08-gestor` (US24).

| Teste | US |
| --- | --- |
| O administrador configura a regra de comissão do funcionário | US22 |
| O funcionário vê a comissão acumulada no período | US23 |
| O gestor vê o relatório de comissões e o ranking e exporta | US24 |

### Comandos para rodar

=== "Localhost"

    ```bash
    E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e -- comissoes
    ```

=== "Homologação"

    ```bash
    node --env-file=../homolog-e2e.env e2e/run.js comissoes
    ```

### Vídeo da execução

<video controls preload="metadata" width="100%" src="../../assets/videos/comissoes.mp4"></video>

## Estoque

O cadastro de um item até o histórico das movimentações dele. Arquivo `05-estoque`.

| Teste | US |
| --- | --- |
| O administrador cria uma categoria e cadastra um item nela | US13 |
| O funcionário registra entrada e saída, e o saldo acompanha | US14 |
| O item abaixo do mínimo aparece no aviso do painel e no filtro de alertas | US15 |
| O gestor consulta o histórico de movimentações do item | US16 |

### Comandos para rodar

=== "Localhost"

    ```bash
    E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e -- estoque
    ```

=== "Homologação"

    ```bash
    node --env-file=../homolog-e2e.env e2e/run.js estoque
    ```

### Vídeo da execução

<video controls preload="metadata" width="100%" src="../../assets/videos/estoque.mp4"></video>

## Gestão

O gestor acompanha os números do período e leva os relatórios para a reunião. Arquivo `08-gestor` (US11 e US12).

| Teste | US |
| --- | --- |
| O gestor vê o dashboard e troca o período | US11 |
| O gestor exporta o período em PDF e em planilha | US12 |

### Comandos para rodar

=== "Localhost"

    ```bash
    E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e -- gestao
    ```

=== "Homologação"

    ```bash
    node --env-file=../homolog-e2e.env e2e/run.js gestao
    ```

### Vídeo da execução

<video controls preload="metadata" width="100%" src="../../assets/videos/gestao.mp4"></video>
