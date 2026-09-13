# Definition of Ready & Definition of Done

Duas checklists curtas que funcionam como "portões de qualidade" no [quadro Kanban](quadro-kanban.md): a **DoR** decide se uma US pode sair de `Backlog` para `Ready`, e a **DoD** decide se ela pode ir de `Em revisão` para `Concluído`.

A ideia da DoD vem do próprio [Scrum Guide](https://scrumguides.org/) (Schwaber & Sutherland, 2020), que a define como um compromisso formal de qualidade do Incremento. A DoR não é formalmente parte do Scrum Guide, mas é prática amplamente adotada e documentada por autores como Roman Pichler (*Agile Product Management with Scrum*) e Blaine Rubin (*Essential Scrum*) para evitar que o time comece a trabalhar em histórias mal compreendidas.

## Definition of Ready (DoR)

Uma US só entra em **Ready** (fica disponível para ser puxada para desenvolvimento) quando:

- [ ] Está escrita no formato "Como `<ator>`, quero `<ação>`, para `<benefício>`".
- [ ] Tem [critérios de aceite](criterios-aceite.md) escritos e claros.
- [ ] O time entende a história o suficiente para estimar o esforço (mesmo que a estimativa seja aproximada).
- [ ] Dependências conhecidas (outra US, endpoint de API, decisão de design) estão identificadas — e, se bloqueantes, já resolvidas ou combinadas.
- [ ] Está associada a um Épico e tem uma Prioridade definida no quadro.
- [ ] Não depende de uma decisão externa ainda em aberto (ex.: aprovação de layout, regra de negócio pendente).

Se qualquer item falhar, a história volta para `Backlog` para ser refinada antes de avançar.

## Definition of Done (DoD)

Uma US só vai para **Concluído** quando:

- [ ] Todos os critérios de aceite foram validados (manualmente ou por teste automatizado).
- [ ] O código foi revisado e aprovado em Pull Request por pelo menos um outro membro do time.
- [ ] Não há erros de lint, build ou testes quebrados na branch principal após o merge.
- [ ] Foi testado manualmente no ambiente local (rodando via Docker, conforme os READMEs de `Frontend`/`Backend`) cobrindo o caminho feliz e pelo menos um caso de erro relevante.
- [ ] Documentação relevante foi atualizada, se a mudança afeta algo documentado (README, esta documentação, variáveis de ambiente, etc.).
- [ ] A Issue/PR está vinculada à US correspondente e foi fechada automaticamente pelo merge (ou fechada manualmente com referência).

## Por que ter as duas

- A **DoR** evita o retrabalho de começar a codar algo mal compreendido, que vai gerar dúvidas no meio do caminho ou retrabalho.
- A **DoD** evita o "está pronto, mas..." — garante um nível mínimo e consistente de qualidade antes de considerar qualquer história finalizada, independentemente de quem a implementou.

Essas checklists devem ser revisadas periodicamente pelo time: se um critério está sendo sistematicamente ignorado ou é irrelevante na prática, ajuste a lista — DoR e DoD são vivos, não engessados.
