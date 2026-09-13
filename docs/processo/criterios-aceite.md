# Critérios de Aceite

Critérios de aceite traduzem uma User Story em condições objetivas e testáveis: definem quando a história pode ser considerada implementada corretamente. Sem eles, "pronto" vira uma opinião; com eles, vira um checklist verificável — tanto por quem desenvolve quanto por quem revisa.

## Formato recomendado: Given/When/Then

Usamos o formato **Dado / Quando / Então** (Given/When/Then), popularizado pela prática de BDD (*Behavior-Driven Development* — Wynne & Hellesoy, *The Cucumber Book*, 2012). Ele força a pensar em: estado inicial, ação do usuário, resultado esperado.

```
- [ ] Dado que <contexto/estado inicial>,
      quando <ação realizada>,
      então <resultado esperado>.
```

Cada critério deve ser:

- **Específico e testável** — dá para verificar objetivamente se passou ou não.
- **Independente de implementação** — descreve comportamento, não a solução técnica.
- **Pequeno** — um critério, uma verificação. Prefira vários critérios curtos a um critério longo com "e" no meio.

Essa disciplina de escrever histórias pequenas, testáveis e negociáveis segue o mnemônico **INVEST** (Cohn, *Agile Estimating and Planning*, 2005): *Independent, Negotiable, Valuable, Estimable, Small, Testable*.

## Onde registrar

Os critérios de aceite ficam na própria **Issue do GitHub** que representa a US (não neste site de documentação), logo abaixo da descrição da história, numa seção `## Critérios de Aceite`. Isso mantém a checklist junto de onde o trabalho é rastreado (commits, PRs, comentários de revisão).

O arquivo [`historias_usuarios.md`](../historias_usuários.md) continua sendo a fonte da narrativa de cada história; ao transformar uma US em Issue, copie o texto da história e adicione os critérios de aceite específicos.

## Exemplos aplicados

### US01 — Galeria de portfólio

> Como visitante do site, quero visualizar uma galeria de serviços de pintura eletrostática já realizados, para avaliar a qualidade do trabalho da empresa antes de contratar.

**Critérios de Aceite:**

- [ ] Dado que estou na página da galeria, quando a página carrega, então vejo uma lista de projetos com ao menos uma imagem de capa cada.
- [ ] Dado que a galeria tem mais itens do que cabem em uma página, quando eu chego ao fim da lista, então consigo carregar mais itens (paginação ou scroll infinito).
- [ ] Dado que a imagem de um projeto ainda não carregou, quando a página está carregando, então vejo um estado de placeholder (não um espaço vazio quebrado).

### US06 — Contato pessoa física

> Como visitante pessoa física, quero preencher um formulário de contato simples com meus dados e a descrição do que preciso, para ser direcionado a um atendente humano.

**Critérios de Aceite:**

- [ ] Dado que preencho nome, e-mail, telefone e mensagem corretamente, quando envio o formulário, então recebo uma confirmação de que o contato foi enviado.
- [ ] Dado que deixo um campo obrigatório em branco, quando tento enviar, então vejo uma mensagem de erro indicando qual campo precisa ser preenchido, sem perder o que já preenchi.
- [ ] Dado que informo um e-mail em formato inválido, quando tento enviar, então vejo uma mensagem de erro específica para esse campo.

Ao refinar as demais USs (US02 a US26), siga o mesmo padrão: descreva o "caminho feliz" primeiro, depois os casos de erro/borda mais relevantes para aquela história.
