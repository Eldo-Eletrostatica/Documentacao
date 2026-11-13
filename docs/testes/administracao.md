# Administração

O fluxo `administracao` cobre o que só o administrador faz: entrar na área interna, cuidar das contas, ajustar a tabela do orçamento automático e mudar o que o visitante vê no site. Arquivo `04-administrador`. A preparação e as variáveis estão em [Como rodar](index.md).

Tudo o que o teste muda no site público, ele desfaz no fim: a identidade visual volta à anterior, e o projeto e o evento de teste são removidos ou restaurados.

| Teste | US |
| --- | --- |
| Erra a senha, vê o aviso e entra | US09 |
| Cadastra, edita e exclui um funcionário | US10 |
| O funcionário comum não vê as telas de gestão nem de administração | US10 |
| Cadastra e remove um tipo de pintura na tabela de orçamento | US08 |
| Muda o logotipo no rascunho, pré-visualiza e descarta | US25, US26 |
| Publica um projeto no portfólio e o remove | US27 |
| Publica a identidade visual, confere no site e volta à anterior | US25 |
| Publica um evento e o confere na página de eventos | US27 |

## Comandos para rodar

=== "Localhost"

    ```bash
    E2E_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/postgres npm run test:e2e -- administracao
    ```

=== "Homologação"

    ```bash
    node --env-file=../homolog-e2e.env e2e/run.js administracao
    ```

## Vídeo da execução

<video controls preload="metadata" width="100%" src="../../assets/videos/administracao.mp4"></video>
