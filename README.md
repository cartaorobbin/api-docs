# Robbin API Docs

Documentação pública das APIs da Robbin, hospedada no [Mintlify](https://mintlify.com).

Cobre dois BFFs:

- **`bff-partner`** — integração com parceiros externos (Cantu, Chilli, Moura, etc).
- **`bff-backoffice`** — backend das telas internas de backoffice.

> O `bff-app` (usado pelo aplicativo mobile) **não** é documentado aqui.

Contexto completo da decisão arquitetural está em [`prd.md`](./prd.md).

## Estrutura

```
api-docs/
├─ docs.json                # config do Mintlify (dropdowns Partner/Backoffice)
├─ introduction.mdx         # landing
├─ partner/                 # docs da API de Parceiros (externa)
│  ├─ introduction.mdx
│  ├─ quickstart.mdx
│  ├─ authentication.mdx
│  └─ changelog.mdx
├─ internal/                # docs da API de Backoffice (interna)
│  ├─ introduction.mdx
│  ├─ authentication.mdx
│  └─ changelog.mdx
└─ logo/
   ├─ light.svg
   └─ dark.svg
```

A **referência da API** (lista de endpoints, schemas, etc) é gerada
automaticamente pelo Mintlify a partir do `openapi.json` exposto por cada BFF —
configurado no `docs.json` em cada dropdown.

## Desenvolvimento local

```bash
npm i -g mint
mint dev
```

Abre em `http://localhost:3000`.

Para checar links quebrados:

```bash
mint broken-links
```

## Deploy

Conectado ao Mintlify via GitHub App. Push na `main` dispara deploy automático.

## Como contribuir

- **Mudança de contrato externo** (`bff-partner`) → atualize `partner/changelog.mdx` no mesmo PR que mexe no BFF.
- **Mudança no `bff-backoffice`** → atualize `internal/changelog.mdx`.
- **Mudança de OpenAPI** → o site é re-gerado automaticamente; nada a fazer manualmente.
- **Decisões arquiteturais maiores** → abra um RFC em `cartaorobbin/ganesha`.

Veja [`CONTRIBUTING.md`](./CONTRIBUTING.md) para o fluxo de PR.
