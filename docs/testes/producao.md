# Produção

O fluxo `producao` confere o site que os clientes usam depois de uma promoção da `main` para a `producao`. Ele roda à mão e só lê: navega e confere as telas, sem enviar formulário nem salvar nada, e o banco não é copiado nem restaurado ([ADR 0009](../adr/0009-homologacao-antes-da-producao.md)).

O `e2e/run.js` reconhece o endereço da produção e recusa qualquer outro fluxo, assim como um `E2E_DATABASE_URL`. A suíte completa continua só na homologação.

| Teste | US |
| --- | --- |
| Vê o portfólio e filtra por categoria | US01 |
| Abre o detalhe de um projeto | US02 |
| Vê os eventos e abre um registro | US03 |
| Vê as avaliações do Google ou o aviso de indisponível | US05 |
| A página de orçamento carrega o envio de PDF e o simulador, sem enviar | US07 |
| Um protocolo inexistente mostra o aviso | US19 |
| O gestor de teste entra e abre as telas da área interna, sem salvar nada | US09 |
| O site público cabe na tela do celular | |

## Conta de gestor de teste

O login usa uma conta só para os testes, nunca a do administrador da Eldo. Ela é criada uma vez, pelo administrador, em **Área interna → Funcionários**, com o perfil **Gestor**, e aparece na lista de funcionários.

## Comandos para rodar

Os dados da conta ficam num arquivo fora dos repositórios, por exemplo `prod-e2e.env`, ao lado das pastas `Frontend`, `Backend` e `Documentacao`. Nunca o commite.

```env
E2E_BASE_URL='https://eldo-site.onrender.com'
E2E_MANAGER_EMAIL='<e-mail do gestor de teste>'
E2E_MANAGER_PASSWORD='<senha do gestor de teste>'
```

=== "Produção"

    ```bash
    node --env-file=../prod-e2e.env e2e/run.js producao
    ```

=== "GitHub Actions"

    **Actions → Selenium na produção → Run workflow**, no repositório `Frontend`. Roda sem tela, com os testes da branch `producao`, e usa os segredos `PROD_E2E_MANAGER_EMAIL` e `PROD_E2E_MANAGER_PASSWORD`.

=== "Localhost"

    ```bash
    npm run test:e2e -- producao
    ```

    Fora da produção, o fluxo cria o gestor de teste da suíte, como os outros.
