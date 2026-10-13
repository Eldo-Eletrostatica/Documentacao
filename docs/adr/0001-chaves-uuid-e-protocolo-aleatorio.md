# Chaves UUID e protocolo aleatório na OS

Todas as tabelas usam UUID como chave primária, e a OS tem, além disso, um protocolo aleatório de 8 caracteres (sem os confundíveis 0/O e 1/I) para a consulta pública da US19. Com chaves sequenciais, quem vê uma OS deduz a existência das vizinhas; como essa consulta não tem login, o código usado nela precisa ser impossível de adivinhar.

## Considered Options

- **ID inteiro sequencial:** mais simples de ler e depurar, mas expõe a quantidade e a ordem dos registros em qualquer URL ou consulta.
- **Protocolo sequencial legível** (`OS-2026-0042`): fácil de falar ao telefone, mas quem tem um protocolo descobre os outros trocando o número.

## Consequences

A consulta pública também aceita o CNPJ do cliente. Como o CNPJ é público, essa forma de consulta mostra só a etapa e o prazo das OS em andamento, sem valores nem descrição do serviço.
