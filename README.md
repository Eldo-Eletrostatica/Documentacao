# Documentacao

Documentação do projeto Eudo Eletrostática, gerada com [MkDocs](https://www.mkdocs.org/) + tema [Material](https://squidfunk.github.io/mkdocs-material/).

## Setup

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Uso

Servidor local com live-reload:

```bash
mkdocs serve
```

Build estático (saída em `site/`):

```bash
mkdocs build
```

## Estrutura

- `mkdocs.yml` — configuração do site e navegação
- `docs/` — páginas em Markdown
