# Stack Tecnológica

Frontend e Backend vivem em repositórios separados e se comunicam exclusivamente via **API REST**.

## Frontend

| | Tecnologia | Papel no projeto |
| --- | --- | --- |
| :simple-nextdotjs: | **[Next.js](https://nextjs.org/)** | Framework React (App Router) — páginas, rotas e renderização do site |
| :simple-react: | **[React](https://react.dev/)** | Biblioteca de UI sobre a qual o Next.js é construído |
| :simple-typescript: | **[TypeScript](https://www.typescriptlang.org/)** | Tipagem estática em todo o código do frontend |
| :simple-eslint: | **[ESLint](https://eslint.org/)** | Padronização e qualidade do código |

## Backend

| | Tecnologia | Papel no projeto |
| --- | --- | --- |
| :simple-nestjs: | **[NestJS](https://nestjs.com/)** | Framework Node.js estruturado em MVC (Controller → Service → Model) |
| :simple-nodedotjs: | **[Node.js](https://nodejs.org/)** | Runtime JavaScript/TypeScript do backend |
| :simple-typescript: | **[TypeScript](https://www.typescriptlang.org/)** | Tipagem estática em todo o código do backend |
| :simple-typeorm: | **[TypeORM](https://typeorm.io/)** | ORM — mapeia entidades para tabelas do PostgreSQL (o "Model" do padrão MVC) |
| :simple-swagger: | **[Swagger](https://swagger.io/)** | Documentação interativa da API, gerada automaticamente (`/api`) |
| :simple-jest: | **[Jest](https://jestjs.io/)** | Testes unitários e end-to-end |
| :simple-eslint: | **[ESLint](https://eslint.org/)** | Padronização e qualidade do código |
| :simple-prettier: | **[Prettier](https://prettier.io/)** | Formatação automática do código |

## Banco de Dados

| | Tecnologia | Papel no projeto |
| --- | --- | --- |
| :simple-postgresql: | **[PostgreSQL](https://www.postgresql.org/)** | Banco de dados relacional principal |

## Infraestrutura

| | Tecnologia | Papel no projeto |
| --- | --- | --- |
| :simple-docker: | **[Docker](https://www.docker.com/)** / Docker Compose | Ambiente de desenvolvimento containerizado — cada repositório (Frontend, Backend) tem seu próprio `Dockerfile` e `docker-compose.yml` |
| :simple-github: | **[GitHub](https://github.com/)** | Hospedagem dos repositórios e organização (`Eldo-Eletrostatica`) |
| :simple-githubactions: | **[GitHub Actions](https://github.com/features/actions)** | CI/CD — hoje usado para publicar esta documentação no GitHub Pages a cada push |
| :simple-npm: | **[npm](https://www.npmjs.com/)** | Gerenciador de pacotes de Frontend e Backend |

## Documentação

| | Tecnologia | Papel no projeto |
| --- | --- | --- |
| :simple-materialformkdocs: | **[MkDocs Material](https://squidfunk.github.io/mkdocs-material/)** | Geração deste site de documentação, publicado no GitHub Pages |
| :simple-markdown: | **Markdown** | Formato de todo o conteúdo desta documentação e das User Stories |
