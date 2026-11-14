# Homologação antes da produção

Toda mudança passa por um ambiente de homologação antes de chegar à produção. A `main` é publicada na homologação, e a produção publica a branch `producao`, que só recebe a `main` por PR, depois que alguém do grupo valida a mudança na homologação.

```text
branch da tarefa ──PR──> main ──(Render)──> homologação
                                                │ alguém do grupo valida
                     producao <──PR de promoção──┘
                         └──(Render)──> produção
```

- **Branches:** `main` e `producao` nos repositórios Frontend e Backend, as duas protegidas: só recebem PR, com o CI verde. Qualquer pessoa do grupo pode aprovar o PR de promoção.
- **Publicação:** o Render publica um commit só depois que o CI dele passa (`autoDeployTrigger: checksPass`). O CI roda nas duas branches, porque o PR de promoção cria um commit novo na `producao`.
- **Homologação:** instâncias grátis do Render (`render.homolog.yaml` do Backend), banco num projeto grátis do Neon e bucket próprio no R2 (`eldo-homolog`). O widget do Turnstile é o mesmo da produção, com o hostname da homologação acrescentado.
- **Suíte Selenium:** a suíte completa roda só na homologação, porque cria contatos, vendas e funcionários e restaura o banco no fim. O `E2E_CAPTCHA_TOKEN`, que libera os formulários públicos do CAPTCHA e do limite por IP, existe só lá.
- **Selenium na produção:** só o fluxo `producao`, rodado à mão depois de uma promoção. Ele navega e confere as telas sem enviar formulário nem salvar nada, entra na área interna com uma conta de gestor criada só para os testes, e o banco não é copiado nem restaurado. O script recusa qualquer outro fluxo no endereço da produção.
- **Desenvolvimento:** continua local, com o `docker compose` de cada repositório.

## Considered Options

- **PR na `main` direto para a produção:** mais simples, mas não deixa momento para validar antes de o cliente ver a mudança, e a suíte Selenium teria de rodar contra os dados reais da Eldo.
- **Suíte Selenium completa também na produção:** restaurar o banco apagaria o que clientes fizessem durante a execução, os formulários mandariam e-mails reais à Eldo e precisariam do `E2E_CAPTCHA_TOKEN`, e os testes de administração mudariam o site que os clientes veem.
- **Três ambientes (`develop`, homologação e produção):** muda o fluxo do grupo para o Git Flow e acrescenta um merge a cada entrega, sem ganho para um site do tamanho da Eldo.
- **Promoção por release (tag):** registra versões, mas exige um workflow e um deploy hook do Render guardado como segredo no GitHub.
- **Banco grátis do Render na homologação:** expira em 30 dias. Recriá-lo automaticamente exigiria guardar no GitHub uma chave da API do Render, com acesso à conta inteira, inclusive à produção.

## Consequences

- Uma mudança só chega ao cliente depois de dois PRs: o da tarefa e o de promoção.
- A homologação dorme depois de 15 minutos sem acesso; o primeiro acesso de cada validação leva cerca de 1 minuto.
- A homologação custa US$ 0. O Neon conecta pela internet, e a API ganhou a opção `DB_SSL` para isso.
- Os avisos por e-mail não saem da homologação, porque um serviço grátis do Render não envia nas portas do SMTP.
- A conta de gestor de teste aparece na lista de funcionários da produção.
- Uma correção urgente segue o mesmo caminho. Para voltar atrás em produção, o Render tem o Rollback de cada serviço.
