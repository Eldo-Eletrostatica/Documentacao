# Hospedagem e custos

O site vai para o [Render](https://render.com/), com o PostgreSQL gerenciado do próprio Render, os arquivos no Cloudflare R2 (ADR 0006) e o CAPTCHA no Cloudflare Turnstile. O custo previsto é de cerca de **US$ 21,50 por mês**, todo no Render. Os planos foram testados com carga, e atendem a Eldo com folga (ver [Testes de carga](testes-de-carga.md)).

Os valores e as regras abaixo foram conferidos em 11/11/2026. Preços e limites de plano grátis mudam: confira de novo antes de contratar.

## Arquitetura

```text
navegador ──HTTPS──> Frontend (web service, público)
                        │  /api/* repassado pelo proxy do Next (ADR 0007)
                        │  páginas do servidor buscam direto pela API_URL
                        ▼
                     Backend (private service, sem acesso pela internet)
                        ├──> PostgreSQL (Render)
                        ├──> Cloudflare R2: imagens e PDFs (ADR 0006)
                        ├──> Cloudflare Turnstile: validação do CAPTCHA
                        ├──> SMTP: avisos de contato e orçamento (US28)
                        └──> Google Places: avaliações (US05)
```

O Backend fica como **private service**, que só recebe tráfego da rede privada do Render. O navegador nunca fala com ele: tudo passa pelo proxy `/api` do Frontend. Além de esconder a API da internet, isso é exigido pelo limite de envios por IP, que lê o IP do visitante no `X-Forwarded-For` acrescentado pelo proxy. Com a API pública, qualquer pessoa poderia mandar um `X-Forwarded-For` falso e burlar o limite.

Os três serviços precisam estar na mesma região, porque a rede privada só funciona assim.

## Planos e custo mensal

| Serviço | Plano no Render | CPU | Memória | US$/mês |
| --- | --- | --- | --- | --- |
| Frontend | Web service Starter | 0,5 | 512 MB | 7,00 |
| Backend | Private service Starter | 0,5 | 512 MB | 7,00 |
| PostgreSQL | Basic | 0,1 | 256 MB | 6,00 |
| Disco do PostgreSQL | 5 GB | | | cerca de 1,50 |
| Workspace | Hobby | | | 0,00 |
| **Total** | | | | **cerca de 21,50** |

Os preços dos planos são os do painel do Render. O do disco vem de um guia do Render que cita US$ 0,30 por GB por mês, e pode estar desatualizado. A cobrança é em dólar no cartão, com câmbio e IOF.

O plano do workspace (Hobby, Pro) e o tipo de instância de cada serviço (Free, Starter) são cobrados separadamente. O Hobby basta: ele aceita um membro e até 25 serviços.

### Disco do PostgreSQL

**5 GB, com o autoscaling ligado.**

- O banco de desenvolvimento, com os dados de demonstração, ocupa 8,8 MB. O banco guarda só texto e URLs: imagens e PDFs ficam no R2.
- Mesmo com 5 mil pedidos e 20 mil movimentações de estoque por ano, o crescimento fica abaixo de 100 MB por ano.
- Os dados caberiam em 1 GB. A folga é para o WAL (o log de transações do PostgreSQL, que pode crescer até 1 GB em momentos de muita escrita) e porque, com o disco cheio, o Render suspende o banco e o site para.
- O disco só cresce: dá para aumentar depois, nunca diminuir.
- Com o autoscaling, o Render aumenta o disco em 50% quando ele passa de 90% de uso.

## Serviços externos

| Serviço | Para quê | Custo esperado |
| --- | --- | --- |
| Cloudflare R2 | Imagens do site e PDFs dos orçamentos | Zero no volume da Eldo: o plano grátis tem 10 GB, 1 milhão de escritas e 10 milhões de leituras por mês, sem cobrança de saída. Exige cartão para ativar |
| Cloudflare Turnstile | CAPTCHA dos formulários públicos | Grátis |
| Google Places | Avaliações do Google na página inicial (US05) | 1.000 consultas grátis por mês na categoria que traz as avaliações; depois, US$ 25 por mil. Exige conta de faturamento no Google Cloud, com cartão |
| SMTP | Avisos por e-mail para a Eldo (US28) | Zero com o e-mail da própria empresa (Gmail com senha de app, porta 587) |

## Contas

As contas pagas ficam **no nome da Eldo, com o cartão da Eldo**: Render, Cloudflare (R2 e Turnstile) e Google Cloud. Assim, os dados dos clientes ficam sob controle da empresa, o que pesa pela LGPD, a cobrança não passa por ninguém do grupo e o site continua no ar depois da disciplina.

As contas foram criadas com um e-mail que será entregue ao cliente. Esse e-mail recupera a senha de todas as outras, então trocar a senha dele troca o acesso a tudo. A verificação em duas etapas deve ficar no celular de alguém da Eldo, não de alguém do grupo.

## Plano grátis do Render

Serve para testes, não para a Eldo usar no dia a dia:

- um web service Free dorme depois de 15 minutos sem acesso, e o primeiro acesso seguinte leva cerca de 1 minuto;
- o PostgreSQL Free expira 30 dias depois de criado, com mais 14 dias para passar a um plano pago antes de os dados serem apagados;
- um web service Free não envia nas portas 25, 465 e 587, as do SMTP, então os avisos por e-mail não saem;
- private service não tem instância Free, e um web service Free não recebe tráfego da rede privada.

Por isso o deploy foi direto para os planos pagos, descritos no `render.yaml` do Backend: um Blueprint que cria a API, o site e o banco de uma vez (ver a seção "Deploy no Render" do [README do Backend](https://github.com/Eldo-Eletrostatica/Backend#deploy-no-render)). O site acha a API pela `API_HOSTPORT`, o endereço dela na rede privada, que o próprio Render preenche quando cria a API.

## Homologação

Antes de chegar à produção, toda mudança passa pela homologação (ver [ADR 0009](../adr/0009-homologacao-antes-da-producao.md)). A `main` é publicada lá, e a produção publica a branch `producao`, que recebe a `main` por PR depois que alguém do grupo valida a mudança. O caminho completo de um commit está em [Pipeline de deploy e testes](pipeline.md).

| Peça | Onde | US$/mês |
| --- | --- | --- |
| Site e API | Render, instâncias Free (`render.homolog.yaml` do Backend) | 0,00 |
| PostgreSQL | Neon, plano grátis, projeto `eldo-homolog` na região AWS us-east-1 | 0,00 |
| Imagens e PDFs | Cloudflare R2, bucket `eldo-homolog` | 0,00 |
| CAPTCHA | O mesmo widget do Turnstile, com o hostname da homologação | 0,00 |

O banco fica no Neon porque o PostgreSQL grátis do Render expira em 30 dias, e o do Neon não: o plano grátis tem 1 GB e 100 horas de computação por mês, e o banco suspende depois de 5 minutos sem uso. Como o Neon fica fora da rede privada do Render, a API conecta com SSL (`DB_SSL=true`).

Valem os limites do plano grátis do Render descritos acima: o site e a API dormem, a API fica pública e os avisos por e-mail não saem. A suíte Selenium completa roda só na homologação, com o `E2E_CAPTCHA_TOKEN`, que não existe na produção; na produção roda só o fluxo `producao`, de leitura, à mão.

## Variáveis de produção

As listas completas estão nos READMEs do [Backend](https://github.com/Eldo-Eletrostatica/Backend#imagem-de-produção) e do [Frontend](https://github.com/Eldo-Eletrostatica/Frontend#imagem-de-produção). Desde o Backend#67, com `NODE_ENV=production`, a API se recusa a subir quando:

- o `JWT_SECRET` é o do `.env.example` ou tem menos de 32 caracteres;
- falta o `SMTP_HOST` ou o `NOTIFICATION_EMAIL`;
- o `TURNSTILE_SECRET_KEY` é uma chave de teste da Cloudflare.

O Swagger fica fora do ar em produção; `ENABLE_SWAGGER=true` o liga.

No Frontend, a `API_URL`, a `STORAGE_PUBLIC_URL` e a `NEXT_PUBLIC_TURNSTILE_SITE_KEY` entram no build. O Render repassa as variáveis do serviço ao `docker build` como build args, então basta cadastrá-las no painel. O widget do Turnstile precisa ter como hostname o endereço exato do Frontend (por exemplo, `eldo-site.onrender.com`), e depois o domínio próprio da Eldo, se houver.

## Pendências antes do deploy

O Render constrói o último estágio do `Dockerfile` e não aceita `--target`. No Backend, o `prod` passou a ser o último estágio, e o `docker-compose.yml` escolhe o `dev` pelo nome. No Frontend, o `prod` já era o último.

- **IP do visitante atrás do Render.** No Render, o Frontend fica atrás de um balanceador. Se o proxy do Next acrescentar ao `X-Forwarded-For` o IP do balanceador, e não o do visitante, todos os visitantes contam como um IP só, e o site inteiro fica limitado a 5 contatos por hora e 20 falhas de login por minuto. Conferir nos logs do primeiro deploy.
- **Cartão no Google Cloud.** O cartão virtual da Eldo foi recusado na conta de faturamento do Google. Até a chave existir, a página inicial avisa que as avaliações estão indisponíveis; as avaliações de exemplo ficam só no desenvolvimento, para o visitante não ler depoimentos inventados.
- **Consultas ao Google.** O serviço de avaliações não guarda as respostas do Google, então cada acesso pode virar uma consulta paga depois das 1.000 grátis. Medir antes de ligar a cobrança (Backend#65).
- **Dados que dependem do cliente:** CNPJ e cartão para o Google Cloud e o R2, e-mail oficial para o SMTP e os avisos, domínio próprio, se houver.

## Fontes

- [Render: plano grátis](https://render.com/docs/free)
- [Render: preços](https://render.com/pricing)
- [Render: recursos por plano de workspace](https://render.com/docs/new-workspace-plans)
- [Render: Docker](https://render.com/docs/docker)
- [Render: pedido de seleção de estágio no Docker](https://feedback.render.com/features/p/support-docker-multistage-target-in-renderyaml)
- [Render: criar e conectar ao PostgreSQL](https://render.com/docs/postgresql-creating-connecting)
- [Render: autoscaling do disco do banco](https://render.com/changelog/automatically-increase-your-database-storage-when-running-low)
- [Render: custo de hospedagem para pequenas empresas](https://render.com/articles/how-much-does-cloud-application-hosting-cost-for-small-businesses)
- [Cloudflare R2: preços](https://developers.cloudflare.com/r2/pricing/)
- [Cloudflare Turnstile: hostnames](https://developers.cloudflare.com/turnstile/concepts/hostname-management/)
- [Google Maps Platform: preços](https://developers.google.com/maps/billing-and-pricing/pricing)
- [Neon: preços](https://neon.com/pricing)
