# Quadro de Backlog (GitHub Projects)

Este projeto usa o **GitHub Projects** (a versão atual, conhecida como *Projects v2*) como quadro de acompanhamento do backlog. É gratuito, já integra com os repositórios `Frontend`, `Backend` e `Documentacao`, e cobre bem o fluxo descrito abaixo sem precisar de outra ferramenta.

## Padrão adotado: fluxo Kanban com portões de qualidade

Times pequenos com um backlog relativamente estável (como é o caso aqui, com 26 USs já mapeadas em 9 épicos) costumam se sair melhor com um **fluxo contínuo (Kanban)** do que com Sprints fechadas de Scrum — não há a sobrecarga de cerimônias (planning, sprint review, etc.), mas ainda se mantém disciplina de processo através de "portões" de qualidade nas transições entre colunas.

Esse modelo se baseia em **Kanban** (Anderson, *Kanban: Successful Evolutionary Change for Your Technology Business*, 2010) — fluxo visual, limite de trabalho em progresso (WIP) e "puxar" itens em vez de "empurrar".

### Colunas do quadro

| Coluna (Status) | Significado | Portão de entrada |
| --- | --- | --- |
| **Backlog** | US identificada, ainda não refinada | — |
| **Ready** | Pronta para ser desenvolvida | US refinada e com suas dependências cobertas  |
| **Em andamento** | Alguém está trabalhando nela agora | foi puxada por um membro do time |
| **Em revisão** | Code review / QA / validação dos critérios de aceite | PR aberto vinculado à issue |
| **Concluído** | Feito e validado | testes passando e cliente validou |

## Onde uma US mora: sempre centralizada na `Documentacao`

Como os repositórios (`Frontend`, `Backend`, `Documentacao`) vivem na mesma organização, o Project já agrega issues dos três num quadro só. Ainda assim, adotamos uma regra única e sem exceções para **onde cada US vira issue**: toda US começa como issue na **`Documentacao`** — repo neutro, que já é onde vive `historias_usuários.md` — independentemente de ela envolver um repositório só ou vários. Isso evita ter que decidir caso a caso "essa US é só front, vai direto lá" e mantém as 26 USs sempre listadas num único lugar, espelhando o próprio `historias_usuários.md`.

Quando a implementação precisa de trabalho em `Frontend` e/ou `Backend`, use o recurso de **[sub-issues](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/adding-sub-issues)** do GitHub para conectar a US ao(s) repositório(s) de código:

1. A issue na `Documentacao` (ex.: `US07 - Formulário de orçamento PJ`) contém a história completa e os [critérios de aceite](criterios-aceite.md).
2. Crie uma issue técnica em cada repositório envolvido, descrevendo só a parte daquele lado — ex.: `Frontend: formulário de orçamento (UI)` e/ou `Backend: endpoint de cálculo de orçamento`. Uma US de um repositório só ainda assim ganha ao menos uma sub-issue técnica correspondente.
3. Na issue da `Documentacao`, adicione essa(s) issue(s) como **sub-issues** (botão "Add sub-issue"/"Convert to sub-issue"). O GitHub passa a mostrar uma barra de progresso (ex.: "1/2") na issue pai automaticamente.

Isso evita duplicar a narrativa da US: ela existe **uma vez**, na `Documentacao`; as sub-issues só têm o "como implementar" de cada lado, que é conteúdo diferente por natureza (não repetido). No quadro, tanto a issue da US quanto as sub-issues aparecem como itens.

## Ao criar as issues das USs

Para cada User Story, ao abrir a issue na `Documentacao`:

- Título: `US01 - <resumo curto>`
- Corpo: a história completa + seção **Critérios de Aceite** (ver [página dedicada](criterios-aceite.md))
- Label: o épico correspondente (ex.: `epico:vitrine-portfolio`)

Depois, crie e conecte as sub-issues técnicas em `Frontend`/`Backend` conforme descrito acima — essas só precisam do escopo daquele repo, sem repetir a história completa.
