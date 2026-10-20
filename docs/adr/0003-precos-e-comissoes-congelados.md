# Preço e comissão congelados no momento do cálculo

Cada item do pedido guarda uma cópia do preço por m² do tipo de pintura e do fator do material usados no cálculo, e cada pedido vendido guarda o valor da comissão calculado no lançamento da venda. Os preços de tinta mudam muito (a tinta dourada variou de 4 a 5 vezes em poucos meses), e um orçamento já enviado ou uma comissão já paga não podem mudar de valor quando a tabela de preços ou a regra de comissão mudam.

## Considered Options

- **Recalcular sempre a partir das tabelas atuais:** sem cópia de dados, mas orçamentos e comissões antigos mudariam de valor sozinhos, e os relatórios de pagamento (US24) deixariam de mostrar o que foi realmente devido.

## Consequences

A cópia é uma duplicação proposital: não é falha de normalização a ser corrigida. Para mostrar o valor de um orçamento ou de uma comissão, use os valores guardados no próprio registro, não os do catálogo de preços ou da regra do funcionário.
