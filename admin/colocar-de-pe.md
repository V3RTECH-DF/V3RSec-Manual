---
title: Colocar o V3RSec de pé
nav_order: 1
parent: Admin de Plataforma
---

# Colocar o V3RSec de pe (docker compose)

O V3RSec é empacotado para rodar em um único nó, via `docker compose`, operável por uma
pessoa. Esta página é a sequência de subida real, **verificada em execução** — inclusive os
dois passos que mudaram desde a versão anterior: os certificados do OpenSearch (novo,
obrigatório) e as migrations (agora automáticas).

> **Ordem importa.** Gere os certificados do OpenSearch **antes** da primeira subida, suba
> os containers, depois faça o [bootstrap manual do Logto](bootstrap-logto.md) — sem ele a
> autenticação não funciona. Pular a ordem não quebra sempre na hora; alguns passos faltando
> só aparecem depois, como falha silenciosa.

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
| **Backend / PWA** | `BACKEND_PORT` (8000), `PWA_PORT` (8080) | Portas publicadas no host. São os dois únicos serviços expostos — veja a nota sobre colisão de porta no passo 4. |
| **Fila** | `QUEUE_PASSWORD` | Redis (fila assíncrona de coleta e detecção). |
| **OpenSearch** | `OPENSEARCH_ADMIN_USER`, `OPENSEARCH_ADMIN_PASSWORD`, `OPENSEARCH_URL` | Armazenamento e indexação. |
| **MinIO** | `MINIO_ROOT_USER`, `MINIO_ROOT_PASSWORD` | Object storage on-prem para retenção fria (snapshots). |
| **Logto (IdP)** | `LOGTO_POSTGRES_*`, `LOGTO_ENDPOINT`, `LOGTO_ADMIN_ENDPOINT`, `LOGTO_ISSUER`, `LOGTO_JWKS_URI`, `LOGTO_API_RESOURCE` | Serviço de identidade/login. Ajuste os endpoints para o domínio real em produção. |
| **Logto (PWA)** | `VITE_LOGTO_ENDPOINT`, `VITE_LOGTO_APP_ID`, `VITE_LOGTO_API_RESOURCE`, `VITE_BACKEND_URL` | Lidos pelo aplicativo no build. O `VITE_LOGTO_APP_ID` você obtém no [bootstrap do Logto](bootstrap-logto.md). |
| **Coletor V3RCondo** | `V3RCONDO_AUDIT_KEY`, `V3RCONDO_AUDIT_BASE_URL`, `V3RSEC_COLLECTOR_TENANT_ID`, `V3RSEC_COLLECTOR_TARGET_ID` | Chave read-only da Audit Events API e os IDs do tenant/alvo. Sem eles o worker de coleta não sobe. |

> Segredos ficam fora do código: `.env` e `.envrc` estão no `.gitignore`. Nunca versione o
> arquivo preenchido.

> **Atenção — a senha do OpenSearch tem exigência própria.** `OPENSEARCH_ADMIN_PASSWORD`
> precisa ter maiúscula, minúscula, número e símbolo (mínimo 8 caracteres). Uma senha
> simples não dá erro claro na hora: o container do OpenSearch **recusa subir**, e o sintoma
> que aparece é o serviço reiniciando em loop ou saindo com erro genérico de segurança. Se a
> subida travar no OpenSearch, confira a senha antes de qualquer outra coisa.

## 2. Gerar os certificados TLS do OpenSearch (obrigatório, uma vez)

```bash
./deploy/opensearch/generate-certs.sh
```

A imagem do OpenSearch sobe, por padrão, com certificados de demonstração cuja **chave
privada é pública** — vem embutida na própria imagem, igual para todo mundo que a baixa.
Numa ferramenta de monitoramento de segurança isso é inaceitável mesmo em desenvolvimento
(ambiente de teste tende a virar molde do de produção, e ninguém troca certificado "depois").
Este script gera uma CA e certificados próprios do V3RSec em `deploy/opensearch/certs/`
(gitignored — nenhuma chave privada é versionada) e é o que o `docker-compose.yml` já espera
montar no container.

Rode este passo **antes** da primeira subida. Se você pular, o OpenSearch ainda sobe (ele usa
os certificados de demonstração por padrão), mas com a falha de segurança acima — não é um
atalho seguro, é dívida.

Para acrescentar o hostname de produção ao certificado (além de `opensearch`, `localhost` e
`127.0.0.1`, que já vêm por padrão):

```bash
EXTRA_SAN="DNS:opensearch.v3rsec.exemplo" ./deploy/opensearch/generate-certs.sh
```

Depois de gerar (ou trocar) o certificado, aplique com `docker compose up -d opensearch` (ou
`restart`) — não é preciso recriar o volume de dados do OpenSearch.

## 3. Subir os containers

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

**As migrations do control-plane rodam sozinhas.** O serviço `v3rsec-migrate` aplica o
schema e o seed de papéis/permissões (RBAC) automaticamente na subida, antes do backend
começar a aceitar requisições — não é preciso (e não existe mais o passo de) rodar
`deno task migrate` manualmente. É idempotente: subir de novo não falha nem duplica nada.

## 4. Onde as coisas ficam

- **Backend:** `http://localhost:8000` — nesta fatia, a rota pública é `GET /health`.
- **PWA:** `http://localhost:8080`.
- **Logto (login):** `http://localhost:3301`; **console admin do Logto:**
  `http://admin.localhost:3302` — **não** `http://localhost:3302` puro (veja o motivo no
  [bootstrap do Logto](bootstrap-logto.md)).

> **Atenção — porta ocupada não é realocada sozinha.** Se `BACKEND_PORT` ou `PWA_PORT` já
> estiverem em uso por outro serviço do seu host, o `docker compose up` **falha** com
> `port is already allocated` — o Docker não tenta outra porta por conta própria. Neste
> ambiente, por exemplo, as portas `3001` e `8080` já estavam ocupadas por outras aplicações
> rodando na máquina; a solução foi ajustar `PWA_PORT=8081` no `.env` antes de subir de novo.
> Depois de qualquer ajuste de porta, confira a porta publicada de fato com
> `docker compose ps` (coluna `PORTS`) — é ela, não o valor "padrão" do `.env.example`, que
> você usa para abrir o PWA e para configurar o redirect do Logto (bootstrap, passo 7).

## 5. Fazer o bootstrap do Logto (obrigatório)

Antes de qualquer login funcionar, siga o [Bootstrap do Logto](bootstrap-logto.md). É um
passo **manual e único**: criar a primeira conta admin, a organização, o aplicativo de
login e o recurso de API. Sem ele, o V3RSec sobe mas ninguém entra.

## Limitações conhecidas da subida

- O vínculo alvo → coletor ainda passa por IDs preenchidos no `.env` (onboarding de alvo
  automatizado é um passo futuro).
- As telas do PWA (saúde dos alvos, ocorrências, notificações) sobem com dados de exemplo —
  a coleta real do V3RCondo já grava no OpenSearch, mas a exibição ao vivo nas telas ainda
  está sendo conectada.

Veja o quadro completo em [Estado atual e limitações](../referencia/estado-atual-e-limitacoes.md).
