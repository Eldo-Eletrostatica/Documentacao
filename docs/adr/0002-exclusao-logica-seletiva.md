# Exclusão lógica só onde há histórico

As tabelas que o histórico referencia usam exclusão lógica (`deleted_at`): `employees`, `customers`, `catalog_items` e `inventory_items`. Um registro excluído some das telas, mas pedidos e movimentações antigas continuam apontando para ele. As demais tabelas apagam de verdade, para não acumular registros que nada referencia.

## Considered Options

- **Exclusão lógica em todas as tabelas:** uma regra só, mais fácil de explicar, mas guarda para sempre conteúdo sem valor histórico, como projetos do portfólio e eventos removidos.
- **Exclusão real sempre:** a chave estrangeira impediria excluir, por exemplo, um funcionário que já tem vendas, e a alternativa (apagar em cascata) destruiria o histórico.

## Consequences

Toda consulta nessas tabelas precisa ignorar os registros excluídos. O `@DeleteDateColumn` do TypeORM faz isso automaticamente nas consultas do repositório; em SQL escrito à mão, o filtro `deleted_at IS NULL` precisa ser explícito.
