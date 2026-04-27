# PRD — Robbin API Docs

**Repositório:** `cartaorobbin/api-docs`
**Stack:** Mintlify
**Status:** Em desenvolvimento — setup inicial

---

## 1. Contexto

A Robbin possui três BFFs:

- `bff-app` — usado pelo app da Robbin. **Não será documentado aqui.**
- `bff-partner` — integração com parceiros externos (Cantu, Chilli, Moura, etc).
- `bff-backoffice` — backend de telas internas de backoffice.

É necessário documentar `bff-partner` e `bff-backoffice` em um site público acessível por parceiros e times internos.

---

## 2. Objetivo

Criar um site de documentação único, hospedado no Mintlify, que centralize a referência das APIs `bff-partner` e `bff-backoffice`, com navegação clara entre as duas e geração automática da referência a partir de specs OpenAPI.

---

## 3. Decisões já tomadas

| Tópico | Decisão | Justificativa |
|---|---|---|
| Ferramenta | Mintlify | Já escolhido pelo time |
| Repositório | `cartaorobbin/api-docs` (já criado) | Separado do `cartaorobbin/ganesha` (que é repo de RFCs/ADRs) |
| Estrutura de sites | **Site único com dropdowns** ("Partner" e "Backoffice") | Plano grátis do Mintlify permite só 1 deployment; dropdowns dão sensação de subsites separados (`/partner/...`, `/backoffice/...`) |
| Fonte do OpenAPI | URL pública dos próprios BFFs | Ambos expõem `openapi.json` publicamente; evita pipeline de sync inicial |
| Conteúdo manual | Code samples + Changelog | Definidos como necessários além da referência auto-gerada |
| Plano | Grátis no início, upgrade pro Pro depois | Validar setup antes de pagar |

---

## 4. Decisões pendentes / futuras

- **Upgrade pro Pro:** quando validar o uso, migrar para 2 deployments separadas com domínios distintos (`docs.partners.robbin.com.br` e `docs.internal.robbin.com.br`) e SSO no site interno.
- **RFC no ganesha:** criar RFC formal documentando essa decisão arquitetural antes de consolidar.
- **Sync OpenAPI via CI:** quando os BFFs deixarem de expor publicamente o OpenAPI (especialmente `bff-backoffice`), criar pipeline para baixar via CI e checkar no repo.
- **Mintlify Agent (workflows):** automatizar sync de changelog quando OpenAPI mudar.

---

## 5. Estrutura de pastas

```
api-docs/
├─ docs.json                    # config principal do Mintlify
├─ introduction.mdx             # landing page do site
├─ partner/
│  ├─ introduction.mdx
│  ├─ quickstart.mdx
│  ├─ authentication.mdx
│  └─ changelog.mdx
├─ internal/
│  ├─ introduction.mdx
│  ├─ authentication.mdx
│  └─ changelog.mdx
└─ logo/
   ├─ light.svg
   └─ dark.svg
```

---

## 6. Configuração do Mintlify (`docs.json`)

```json
{
  "$schema": "https://mintlify.com/docs.json",
  "theme": "mint",
  "name": "Robbin APIs",
  "colors": {
    "primary": "#0D9373",
    "light": "#07C983",
    "dark": "#0D9373"
  },
  "favicon": "/favicon.svg",
  "navigation": {
    "dropdowns": [
      {
        "dropdown": "Partner",
        "icon": "handshake",
        "description": "API para integração de parceiros",
        "tabs": [
          {
            "tab": "Documentação",
            "groups": [
              {
                "group": "Introdução",
                "pages": [
                  "partner/introduction",
                  "partner/quickstart",
                  "partner/authentication"
                ]
              },
              {
                "group": "Recursos",
                "pages": ["partner/changelog"]
              }
            ]
          },
          {
            "tab": "Referência da API",
            "openapi": "https://URL-DO-BFF-PARTNER/openapi.json"
          }
        ]
      },
      {
        "dropdown": "Backoffice",
        "icon": "building",
        "description": "API interna de backoffice",
        "tabs": [
          {
            "tab": "Documentação",
            "groups": [
              {
                "group": "Introdução",
                "pages": [
                  "internal/introduction",
                  "internal/authentication"
                ]
              },
              {
                "group": "Recursos",
                "pages": ["internal/changelog"]
              }
            ]
          },
          {
            "tab": "Referência da API",
            "openapi": "https://URL-DO-BFF-BACKOFFICE/openapi.json"
          }
        ]
      }
    ]
  },
  "logo": {
    "light": "/logo/light.svg",
    "dark": "/logo/dark.svg"
  }
}
```

**TODO:** substituir `URL-DO-BFF-PARTNER` e `URL-DO-BFF-BACKOFFICE` pelas URLs reais dos OpenAPI dos BFFs.

---

## 7. Conteúdo MDX a ser produzido

### 7.1 `introduction.mdx` (root — landing)

Apresenta as duas APIs e direciona o usuário ao dropdown correto. Cards com link pra `partner/introduction` e `internal/introduction`.

### 7.2 `partner/introduction.mdx`

Visão geral da API de Parceiros. O que ela permite, casos de uso principais, fluxo end-to-end de integração. Cards de "próximos passos" para Authentication e Quickstart.

### 7.3 `partner/quickstart.mdx`

Passos para fazer a primeira request:
1. Como obter credenciais (onboarding com a Robbin)
2. URLs de ambiente (sandbox + produção)
3. Exemplo de request (cURL) com response esperado

### 7.4 `partner/authentication.mdx`

Como autenticar requisições. Header `Authorization: Bearer ...`, expiração, refresh, escopos.

**TODO:** preencher com info real do `bff-partner` (modelo de auth ainda a confirmar).

### 7.5 `partner/changelog.mdx`

Histórico de mudanças da API de Parceiros. Formato:

```mdx
## YYYY-MM-DD

### Added
- ...

### Changed
- ...

### Deprecated
- ...

### Removed
- ⚠️ BREAKING — ...
```

Atualizado **manualmente** em todo PR que muda contrato externo. Não gerar automaticamente do diff de OpenAPI (vira ruído).

### 7.6 `internal/introduction.mdx`

Versão simplificada — público interno já tem contexto. O que é o backoffice, ambientes, como rodar local.

### 7.7 `internal/authentication.mdx`

Auth interna (provavelmente SSO/JWT corporativo).

### 7.8 `internal/changelog.mdx`

Mesmo formato do partner, mais enxuto.

---

## 8. Ambiente de desenvolvimento local

```bash
npm i -g mint
cd api-docs
mint dev
```

Abre em `http://localhost:3000`.

---

## 9. Deploy

1. Conta no Mintlify (plano grátis).
2. Dashboard → **New deployment**.
3. Conectar repositório `cartaorobbin/api-docs`.
4. Root directory: vazio (root do repo).
5. Deploy automático em cada push na `main`.

---

## 10. Padrões de escrita e OpenAPI

- **Tags do OpenAPI:** agrupar por **domínio de negócio** (`Pedidos`, `Catálogo`, `Pagamentos`), não por nome de controller. Tags viram navegação direta no Mintlify.
- **Code samples:** usar extensão `x-codeSamples` no OpenAPI quando possível (cURL + Node.js como mínimo). Para fluxos multi-request, escrever guia em MDX em `partner/guides/`.
- **Idioma:** português (audiência principal são parceiros brasileiros).
- **Tom partner:** formal, didático, assume que o leitor não conhece a Robbin.
- **Tom internal:** direto, assume contexto.

---

## 11. Riscos e considerações

| Risco | Mitigação |
|---|---|
| BFF ficar fora do ar quebra build do Mintlify (que faz fetch do OpenAPI) | Monitorar; quando crítico, baixar OpenAPI no repo via CI |
| Endpoints internos do `bff-backoffice` ficarem indexados publicamente | Aceito no MVP (URL já é pública); no Pro, mover pra deployment separada com SSO |
| Mintlify Agent gerar changelog ruim | Não automatizar changelog inicialmente — escrever manual |
| Plano grátis limitar features | Upgrade pro Pro quando validado |

---

## 12. Próximos passos de implementação

1. [ ] Criar estrutura de pastas no repo
2. [ ] Adicionar `docs.json` com URLs reais dos OpenAPI
3. [ ] Adicionar logos em `/logo/`
4. [ ] Escrever os 7 MDX (seções 7.1 a 7.8)
5. [ ] Testar `mint dev` localmente
6. [ ] Conectar ao Mintlify e fazer primeiro deploy
7. [ ] Validar com 1-2 parceiros internamente
8. [ ] Criar RFC no `cartaorobbin/ganesha` documentando a decisão
9. [ ] (Futuro) Upgrade pro Pro + separar em 2 deployments
10. [ ] (Futuro) Configurar Mintlify Agent para sync automático

---

## 13. Referências

- Mintlify docs: https://mintlify.com/docs
- Repo: `cartaorobbin/api-docs`
- Repo de decisões (RFC/ADR): `cartaorobbin/ganesha`
- BFFs: `cartaorobbin/bff-partner`, `cartaorobbin/bff-backoffice`