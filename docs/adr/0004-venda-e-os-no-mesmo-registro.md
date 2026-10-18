# Venda e OS no mesmo registro

> Substituída pela ADR 0005, que junta também o orçamento e a solicitação de contato no mesmo registro, a tabela `orders`.

Venda e OS são conceitos diferentes no glossário (o lado comercial e o lado da produção), mas ficam numa mesma tabela, `service_orders`. Toda venda gera exatamente uma OS, e uma relação estritamente um para um pode virar uma tabela só sem ferir a normalização. A tabela leva o nome da OS porque é como OS que o registro passa a maior parte do tempo: com protocolo, status e prazo.

## Considered Options

- **Tabelas `sales` e `service_orders` ligadas um para um:** separa as responsabilidades no banco, mas é uma tabela a mais para um sistema de escopo pequeno.

## Consequences

A OS é criada no lançamento da venda: não existe OS sem venda, então cadastrar o serviço (US17) e lançar a venda (US21) são o mesmo fluxo. As funcionalidades de venda e comissão (US21 a US24) e as de produção (US17 a US20) trabalham sobre o mesmo registro. Os campos da venda (vendedor, valor, comissão, data da venda) e os da produção (status, prazo, data de conclusão) convivem na mesma entity.
