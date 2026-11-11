# Categorias de estoque em tabela

> Muda um ponto da ADR 0005, que guardava a categoria do estoque como texto no item. O resto do modelo enxuto continua valendo.

A categoria de estoque passa a ter a sua tabela, `inventory_categories`, e o item aponta para ela por chave estrangeira. O administrador cria, renomeia e remove categorias, e o cadastro de item escolhe a categoria numa lista. O banco passa a ter 10 tabelas de domínio.

A US13 pede exatamente isso: o administrador gerencia as categorias, a lista do cadastro reflete a mudança, e os itens de uma categoria removida continuam mostrando o nome dela. Com o texto livre, o administrador não tinha como gerenciar nada, e a mesma categoria aceitava grafias diferentes ("Tintas" e "tintas").

- **Remoção que preserva o item:** a categoria removida ganha `deleted_at`, mas o item continua apontando para ela e mostrando o nome. O `deleted_at` é uma coluna comum, sem a exclusão lógica automática do TypeORM, que esconderia a categoria justamente dos itens que ainda a usam. A lista do cadastro filtra a categoria removida na consulta.
- **Nome único sem diferenciar maiúsculas:** índice único em `LOWER(name)`, só entre as categorias não removidas. Recriar uma categoria com o nome de uma removida é permitido.
- **Migration dos dados:** cada categoria em texto já usada vira uma linha, e nomes que só diferem em maiúsculas viram uma só.

## Considered Options

- **Manter o texto livre, com sugestão das categorias já usadas:** é o que a ADR 0005 previa. Mantém 9 tabelas, mas deixa a US13 sem o critério do administrador e não evita grafias duplicadas.
- **Exclusão lógica automática do TypeORM (`@DeleteDateColumn`):** esconderia a categoria removida do item que a usa, contra o critério da US13.

## Consequences

- A US13 passa a ser cumprida por inteiro.
- A categoria do portfólio continua em texto livre, como na ADR 0005.
- Renomear uma categoria muda o nome em todos os itens dela, porque o nome fica só na tabela de categorias.
