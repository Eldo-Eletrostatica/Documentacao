# Eldo Eletrostática

## O que é este projeto

A **Eldo Eletrostática** é uma empresa de pintura eletrostática, e este projeto é o **site institucional** desenvolvido para ela — um sistema completo que vai além de um site de divulgação, cobrindo desde a vitrine pública até a gestão interna da operação.

## O que o sistema faz

O projeto nasce a partir de 26 User Stories organizadas em 9 épicos, cobrindo três frentes:

**Para o visitante e o cliente**

- Galeria de portfólio com fotos de antes/depois dos serviços já realizados
- Divulgação de ações e eventos da empresa
- Depoimentos e avaliações de clientes
- Formulário de contato simples (pessoa física)
- Formulário de orçamento automático (pessoa jurídica), com estimativa de custo instantânea
- Consulta pública do andamento do serviço contratado, por código de protocolo

**Para a equipe interna**

- Login e controle de permissões por nível de funcionário
- Dashboards estatísticos (orçamentos, conversão, serviços mais pedidos) com exportação de relatórios
- Controle de estoque de tintas e insumos, com alertas de estoque mínimo
- Acompanhamento de serviços em produção, com painel de prazos e atrasos
- Lançamento de vendas, cálculo automático de comissões e ranking comercial

**Para o administrador**

- Configuração das regras de cálculo de orçamento
- Personalização visual do site (tema, logotipo, banners), com pré-visualização e restauração da configuração padrão

Veja a lista completa em [Requisitos (User Stories)](historias_usuários.md).

## Como o projeto é construído

Frontend e Backend vivem em repositórios separados, comunicando-se via API REST — veja a [Stack Tecnológica](stack.md) completa. O desenvolvimento segue um fluxo Kanban com portões de qualidade, documentado em [Processo](processo/quadro-kanban.md).

## Navegação

- [Stack Tecnológica](stack.md) — as tecnologias usadas no projeto.
- [Requisitos (User Stories)](historias_usuários.md) — épicos e histórias de usuário do projeto.
- **Processo**:
    - [Quadro Kanban](processo/quadro-kanban.md) — como o backlog é organizado no GitHub Projects.
    - [Critérios de Aceite](processo/criterios-aceite.md) — como transformar uma US em condições testáveis.
