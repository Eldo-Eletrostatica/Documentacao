# Armazenamento de arquivos: SeaweedFS no desenvolvimento e Cloudflare R2 na produção

Imagens (portfólio, eventos, logotipo e banners) e PDFs de orçamento ficam num armazenamento compatível com a API S3, e o banco guarda só a URL de cada arquivo. Em produção o serviço é o Cloudflare R2, num bucket da conta do cliente. Em desenvolvimento e nos testes é o SeaweedFS, que sobe no `docker compose` do Backend junto com o Postgres. O código do Backend usa o mesmo SDK do S3 nos dois ambientes; muda só o valor das variáveis `S3_*`.

O R2 não cobra pela transferência dos arquivos, que é o maior custo de um site com galeria de imagens, e tem 10 GB gratuitos por mês. A conta é do cliente porque os arquivos são dele, a cobrança fica com ele e, como cada cliente do whitelabel tem seu próprio deploy, cada um tem seu próprio bucket.

O SeaweedFS evita que o grupo compartilhe uma chave de nuvem: cada pessoa tem um bucket local, sem conta, e os testes não dependem da internet. A leitura é pública e a escrita exige a chave de desenvolvimento, como no bucket público do R2.

## Considered Options

- **MinIO no desenvolvimento:** era o emulador de S3 mais usado, mas deixou de publicar imagens Docker oficiais e teve a manutenção da versão comunitária descontinuada.
- **Bucket R2 de desenvolvimento numa conta do grupo:** sem emulador, mas a chave secreta circularia entre todos, os testes de cada pessoa cairiam no mesmo bucket e o CI dependeria da conta e da internet.
- **Adobe S3Mock:** o mais simples de subir, mas é um simulador feito para testes, não um servidor de arquivos.
- **Garage:** servidor S3 fiel, mas exige configurar o layout do cluster e as chaves na primeira subida.
- **Disco local do servidor:** sem dependência externa, mas os arquivos se perdem a cada novo deploy em plataformas sem disco persistente e não passam por uma CDN.

## Consequences

- No deploy, o cliente cria a conta na Cloudflare (o R2 pede cartão cadastrado, mesmo no plano gratuito), um bucket com acesso público de leitura e um token de API com escrita nesse bucket. Esses dados vão para as variáveis `S3_*` do servidor, nunca para o repositório.
- Os arquivos de desenvolvimento são só de teste: nada migra do SeaweedFS para o R2.
- A integração com o armazenamento fica num módulo `storage` do Backend, usado pelas USs com upload (US07, US25 e US27). Quando ele existir, o CI passa a subir o SeaweedFS como serviço, como faz com o Postgres.
