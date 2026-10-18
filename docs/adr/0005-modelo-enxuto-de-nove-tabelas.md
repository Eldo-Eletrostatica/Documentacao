# Modelo enxuto de nove tabelas

O banco tem 9 tabelas de domínio: `employees`, `customers`, `orders`, `order_items`, `catalog_items`, `inventory_items`, `stock_movements`, `showcase_items` e `site_settings`. O primeiro modelo tinha 17 tabelas, grande demais para o escopo do projeto. Para chegar a 9, juntamos tabelas que guardavam etapas ou variações do mesmo conceito, sem sair da 3ª forma normal:

- **Pedido único (`orders`):** solicitação de contato, orçamento, venda e OS são etapas do mesmo registro, controladas pelo `status`. As colunas de cada etapa aceitam nulo, e um CHECK exige os dados da venda (vendedor, valor, comissão, protocolo, escopo e prazo) a partir do status `queued`. O pedido de contato do cliente PF tem origem `contact`, e esse cliente pode ficar sem CPF.
- **Catálogo de preços (`catalog_items`):** tipos de pintura e materiais de peça ficam numa tabela, separados pelo `type`. Cada tipo preenche só a sua coluna de preço (`price_per_m2` ou `price_factor`), garantido por CHECK. O item do pedido aponta para o catálogo por duas FKs, `paint_catalog_item_id` e `material_catalog_item_id`.
- **Vitrine (`showcase_items`):** projetos do portfólio e eventos dividem uma tabela, separados pelo `type`, com CHECKs que exigem as colunas de cada tipo.
- **Categorias em texto:** a categoria do portfólio e a do estoque viram uma coluna de texto no próprio registro.
- **Fornecedor em texto:** o item de estoque guarda só o nome do fornecedor. O contato do fornecedor ficou de fora, porque dependeria do fornecedor e não do item, o que quebraria a 3ª forma normal.
- **Comissão no funcionário:** cada funcionário tem no máximo uma regra (percentual ou valor fixo), em `employees`.

## Considered Options

- **Manter as 17 tabelas:** cada conceito do glossário teria a sua tabela, mas várias tabelas guardavam só um nome ou repetiam a estrutura de outra.
- **Chegar a 6 tabelas:** só seria possível cortando funcionalidades das USs ou guardando dados estruturados em JSONB.

## Consequences

- A US22 fica reduzida a uma regra de comissão por funcionário. Regras por tipo de serviço ou por faixa de valor exigiriam voltar com a tabela de regras.
- O banco garante que as FKs do item do pedido existem, mas não que uma aponta para um tipo de pintura e a outra para um material. O service do pedido faz essa verificação.
- A categoria em texto livre aceita grafias diferentes para a mesma categoria. Para evitar isso, a tela de cadastro sugere as categorias já usadas. As categorias do portfólio aparecem em ordem alfabética, sem ordem definida pelo administrador.
- Substitui a ADR 0004: além de venda e OS, o orçamento e a solicitação de contato também ficam no mesmo registro.
