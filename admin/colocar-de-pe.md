---
title: Colocar o V3RSec de pé
nav_order: 1
parent: Admin de Plataforma
---

# Colocar o V3RSec de pe (docker compose)

O V3RSec é empacotado para rodar em um único nó, via `docker compose`, operável por uma
pessoa. Esta página é a sequência de subida no estado atual do projeto.

> **Ordem importa.** Subir os containers é o primeiro passo, mas a autenticação **só passa
> a funcionar** depois do [bootstrap manual do Logto](bootstrap-logto.md). Faça a subida,
> depois o bootstrap, depois valide o login.

## 1. Preparar as variáveis de ambiente

Copie o arquivo de exemplo e preencha os segredos (nenhum valor real vem versionado):

```bash
cp .env.example .env
# edite .env e troque TODOS os "troque-por-..." por segredos fortes
```

### Variáveis essenciais

| Grupo | Variáveis | Para quê |
|---|---|---|
| **Postgres (control-plane)** | `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD` | Banco de metadados (tenants, alvos, usuários, papéis, ocorrências, auditoria). |
| **Backend / PWA** | `BACKEND_PORT` (8000), `PWA_PORT` (8080) | Portas publicadas no host. São os dois únicos serviços expostos. |
| **Fila** | `QUEUE_PASSWORD` | Redis (fila assíncrona de coleta e detecção). |
| **OpenSearch** | `OPENSEARCH_ADMIN_USER`, `OPENSEARCH_ADMIN_PASSWORD`, `OPENSEARCH_URL` | Armazenamento e indexação. A senha exige maiúscula, minúscula, número e símbolo (mín. 8). |
| **MinIO** | `MINIO_ROOT_USER`, `MINIO_ROOT_PASSWORD` | Object storage on-prem para retenção fria (snapshots). |
| **Logto (IdP)** | `LOGTO_POSTGRES_*`, `LOGTO_ENDPOINT`, `LOGTO_ADMIN_ENDPOINT`, `LOGTO_ISSUER`, `LOGTO_JWKS_URI`, `LOGTO_API_RESOURCE` | Serviço de identidade/login. Ajuste os endpoints para o domínio real em produção. |
| **Logto (PWA)** | `VITE_LOGTO_ENDPOINT`, `VITE_LOGTO_APP_ID`, `VITE_LOGTO_API_RESOURCE`, `VITE_BACKEND_URL` | Lidos pelo aplicativo no build. O `VITE_LOGTO_APP_ID` você obtém no [bootstrap do Logto](bootstrap-logto.md). |
| **Coletor V3RCondo** | `V3RCONDO_AUDIT_KEY`, `V3RCONDO_AUDIT_BASE_URL`, `V3RSEC_COLLECTOR_TENANT_ID`, `V3RSEC_COLLECTOR_TARGET_ID` | Chave read-only da Audit Events API e os IDs do tenant/alvo. Sem eles o worker de coleta não sobe. |

> Segredos ficam fora do código: `.env` e `.envrc` estão no `.gitignore`. Nunca versione o
> arquivo preenchido.

## 2. Subir os containers

```bash
docker compose up -d --build
```

Isso sobe backend (Deno), PWA (React+Tailwind), Postgres, Redis, OpenSearch (+ Dashboards),
Logto, CrowdSec, Prometheus/Alertmanager/Blackbox, MinIO e Vector. Apenas **backend** e
**PWA** têm porta publicada no host; os motores OSS ficam na rede interna do compose (o
backend é o único ponto exposto ao PWA).

Para só validar a configuração, sem subir nada:

```bash
docker compose config
```

## 3. Aplicar as migrations do control-plane

```bash
docker compose run --rm v3rsec-backend deno task migrate
```

Isso cria as tabelas de metadados e aplica o seed data-driven de papéis e permissões (RBAC).

## 4. Onde as coisas ficam

- **Backend:** `http://localhost:8000` — nesta fatia, a rota pública é `GET /health`.
- **PWA:** `http://localhost:8080`.
- **Logto (login):** `http://localhost:3301`; **console admin do Logto:** `http://localhost:3302`.

## 5. Fazer o bootstrap do Logto (obrigatório)

Antes de qualquer login funcionar, siga o [Bootstrap do Logto](bootstrap-logto.md). É um
passo **manual e único**: criar a primeira conta admin, a organização, o aplicativo de
login e o recurso de API. Sem ele, o V3RSec sobe mas ninguém entra.

## Limitações conhecidas da subida

- A **validação ponta a ponta** da subida (todos os serviços conversando de fato) ainda
  está pendente — o compose e as configs base estão entregues, mas o app ainda não roda de
  ponta a ponta.
- Em produção, o **trust do certificado do OpenSearch** ainda precisa ser resolvido.
- O vínculo alvo → coletor ainda passa por IDs preenchidos no `.env` (onboarding de alvo
  automatizado é um passo futuro).

Veja o quadro completo em [Estado atual e limitações](../referencia/estado-atual-e-limitacoes.md).
