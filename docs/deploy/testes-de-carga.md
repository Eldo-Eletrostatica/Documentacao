# Testes de carga

As imagens de produção do Frontend, do Backend e do PostgreSQL rodaram com os mesmos limites de CPU e memória dos planos do Render escolhidos para o deploy (ver [Hospedagem e custos](hospedagem-e-custos.md)). Nenhum serviço estourou a memória, e nenhuma requisição falhou, mesmo com uma carga dezenas de vezes maior que o uso esperado da Eldo.

Os testes foram feitos em 11/11/2026, sobre a `main` dos dois repositórios, já com o Backend#67 e o Frontend#57.

## Resultado

| Cenário | Requisições | Pico de memória | Pico de CPU |
| --- | --- | --- | --- |
| Site público: 20 visitantes simultâneos, sem pausa, 30 s por página | 32 a 50 páginas/s, média de 400 a 620 ms, nenhum erro | Frontend: 71 de 512 MB | Frontend: 52% (limite de 50%) |
| Área interna: 10 funcionários simultâneos, sem pausa, 15 s por tela | 40 a 106 req/s, média de 93 a 250 ms, nenhum erro | API: 98 de 512 MB; banco: 83 de 256 MB | API: 28%; banco: 10% (limite de 10%) |
| Exportações: 5 simultâneas de cada relatório, em PDF e planilha | média de 7 a 260 ms, nenhum erro | API: 101 de 512 MB | API: 35% |
| 5 orçamentos com PDF de 10 MB enviados ao mesmo tempo | os 5 aceitos (201), em 2,3 s cada | **API: 231 de 512 MB** | API: 50% (limite de 50%) |

Em repouso, a API ocupa cerca de 75 MB, o Frontend 42 MB e o PostgreSQL 62 MB. Durante os testes, a API abriu poucas conexões no banco, que aceita até 100.

### Por cenário

**Site público.** As páginas montadas no servidor guardam em cache os dados da API (portfólio, eventos, opções do simulador), então a carga quase não chega ao Backend: a API ficou em 0% de CPU. O Frontend usou toda a CPU do plano, e as páginas ficaram mais lentas sem falhar.

| Página | Páginas/s | Média | p99 |
| --- | --- | --- | --- |
| `/` | 32 | 621 ms | 1.072 ms |
| `/eventos` | 44 | 450 ms | 709 ms |
| `/solicitar-orcamento` | 50 | 400 ms | 707 ms |

**Área interna.** Cada tela foi chamada sem pausa por 10 conexões autenticadas como gestor. O dashboard (`/statistics`) é a consulta mais pesada e chegou a 1,8 s no pior caso (p99), com o banco no limite de 0,1 CPU.

| Rota | Req/s | Média | p99 |
| --- | --- | --- | --- |
| `/statistics` (dashboard) | 40 | 245 ms | 1.815 ms |
| `/service-orders` | 86 | 115 ms | 694 ms |
| `/quotes` | 40 | 248 ms | 998 ms |
| `/contact-requests` | 87 | 113 ms | 601 ms |
| `/sales-rankings` | 106 | 93 ms | 584 ms |

**Exportações.** 25 exportações de cada tipo, 5 de cada vez. A planilha do dashboard é a mais lenta (média de 261 ms); os relatórios de comissão e de ranking saem em menos de 40 ms.

**Orçamento por PDF.** É a operação que mais usa memória, porque o arquivo fica na memória da API até ser gravado no armazenamento e anexado ao aviso por e-mail. Cinco envios simultâneos de 10 MB, o tamanho máximo, levaram a API a 231 MB. O dobro ainda caberia nos 512 MB, e o limite de 10 orçamentos por hora por IP torna esse cenário improvável.

## O que os números dizem

- **Os planos atendem a Eldo com folga.** A empresa terá poucos funcionários na área interna e algumas dezenas de visitas por dia; os cenários acima são dezenas de vezes maiores.
- **A memória, que é o que derruba um serviço, sobra em todos os cenários.** A CPU chega ao limite sob carga pesada, e aí as respostas ficam mais lentas, sem falhar.
- **O PostgreSQL com 0,1 CPU é o recurso mais justo.** Se o dashboard ficar lento com anos de dados, o primeiro passo é subir o plano do banco.

## Como o teste foi feito

- **Imagens:** as de produção (`docker build --target prod`) dos dois repositórios.
- **Limites:** cada container com os limites do plano, aplicados pelo Docker: Frontend e API com `--cpus 0.5 --memory 512m`, PostgreSQL 16 com `--cpus 0.1 --memory 256m`.
- **Dados:** uma cópia do banco de desenvolvimento, com os dados de demonstração (29 pedidos, 18 clientes, 9 itens da vitrine).
- **Carga:** [autocannon](https://github.com/mcollina/autocannon) 8, com conexões simultâneas sem pausa entre as requisições.
- **Medição:** `docker stats` a cada segundo, guardando o pico de cada container.
- **CAPTCHA:** a API de teste rodou com `NODE_ENV=development`, porque em produção ela recusa a chave de teste do Turnstile (Backend#67) e o envio de PDF exige o token. O código executado é o mesmo da produção.

### Limitações

- O teste rodou numa máquina local, com os limites aplicados pelo Docker. A CPU do Render pode ser mais lenta por núcleo; a memória não muda.
- Não há latência de rede entre os serviços, como há entre o navegador e o Render.
- O banco tinha poucos dados. Com anos de pedidos, as consultas do dashboard e dos relatórios devem ficar mais lentas.

## Teste da imagem de produção

Antes da carga, as duas imagens de produção rodaram juntas com um banco vazio, e um roteiro no navegador passou 16 de 16 verificações:

- a API aplicou as 4 migrations do zero e roda com um usuário sem privilégios (`node`);
- o primeiro login criou o administrador inicial, com os cookies `session` e `csrf_token` saindo com `Secure` e `SameSite=Strict`;
- as 11 telas da área interna abriram sem erro;
- uma escrita autenticada pelo cookie e pelo `X-CSRF-Token` passou pelo proxy `/api` (ADR 0007);
- o contato PF foi aceito com o widget real do CAPTCHA.

O teste achou um defeito: no container de produção do Frontend, a `API_URL` não existia, e a página inicial e a de eventos mostravam "Não foi possível carregar". O Frontend#57 corrigiu. O Backend#67 passou a recusar a subida em produção com configuração fraca (ver [Hospedagem e custos](hospedagem-e-custos.md#variaveis-de-producao)).

## Como repetir

Na raiz de cada repositório, gere a imagem de produção e suba os containers com os limites do plano, na rede do `docker compose` do Backend:

```bash
docker build --target prod -t eldo-backend:carga .          # no Backend
docker build --target prod \
  --build-arg API_URL=http://api-carga:3000 \
  --build-arg STORAGE_PUBLIC_URL=http://localhost:8333/eldo \
  --build-arg NEXT_PUBLIC_TURNSTILE_SITE_KEY=1x00000000000000000000AA \
  -t eldo-frontend:carga .                                  # no Frontend

docker run -d --name banco-carga --network backend_default --cpus 0.1 --memory 256m \
  -e POSTGRES_PASSWORD=postgres postgres:16-alpine
docker run -d --name api-carga --network backend_default --cpus 0.5 --memory 512m \
  -p 3201:3000 --env-file <variaveis-da-api> eldo-backend:carga
docker run -d --name site-carga --network backend_default --cpus 0.5 --memory 512m \
  -p 3200:3000 eldo-frontend:carga

npx autocannon@8 -c 20 -d 30 http://localhost:3200/
npx autocannon@8 -c 10 -d 15 -H "Authorization=Bearer <token>" \
  "http://localhost:3201/statistics?startDate=2026-01-01&endDate=2026-12-31"
docker stats
```
