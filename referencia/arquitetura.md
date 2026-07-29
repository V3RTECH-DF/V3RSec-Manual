---
title: Como o V3RSec funciona
nav_order: 1
parent: Referência
---

# Como o V3RSec funciona (arquitetura em linguagem de usuario)

Esta página explica, sem jargão, o caminho que a informação percorre no V3RSec. Entender
esse caminho ajuda você a interpretar as telas e a confiar (ou desconfiar) do que vê.

## O caminho de um evento, do alvo ate voce

```
Fontes  →  Coletor  →  OpenSearch  →  Backend V3RSec  →  PWA (voce)
(alvos)    (puxa ou     (armazena e     (severidade,       (saude, ocorrencias,
            recebe)      indexa)         dedup, alerta)      notificacoes)
                                              │
                                              └─►  push · e-mail · webhook · in-app
```

### 1. Fontes (os alvos)

Cada ativo monitorado (V3RCondo, site institucional, site de cursos) emite ou expõe sinais
de segurança. O que cada alvo consegue mostrar é o seu **teto de observabilidade** — e o
V3RSec respeita esse teto: nunca assume um acesso que não tem. O que um alvo não permite
observar é um **ponto cego declarado**, não um erro escondido.

### 2. Coletor

O coletor traz os sinais para dentro do V3RSec, de duas formas conforme a natureza da fonte:

- **Coletor nativo** (um worker em TypeScript) para fontes que exigem "puxar com memória":
  ler página a página, lembrar até onde leu (cursor), não repetir eventos. É o caso da API
  de auditoria do **V3RCondo**.
- **Vector** para fontes de streaming/push, como logs da borda de rede (Cloudflare) e
  webhooks de billing.

Os dois convergem para o mesmo formato de evento e o mesmo armazenamento. A coleta é
contínua, com **prova de execução** — e o silêncio de uma fonte vira alerta.

### 3. Armazenamento (OpenSearch)

Os eventos são guardados e indexados no OpenSearch, **um índice por tenant** (isolamento).
A retenção é **quente 90 dias** (consultável) e **fria até 1 ano** (arquivo para auditoria,
em object storage on-prem).

### 4. Backend V3RSec (o cérebro)

O backend recebe as detecções, define **severidade**, **agrupa** eventos repetidos
(anti-flood/dedup), cria a **ocorrência** e dispara os alertas. É também onde vivem o
**controle de acesso (RBAC)**, o **isolamento por tenant**, o **onboarding de alvo**, a
**gestão de segredos** e a **trilha de auditoria**. O backend é o único ponto que o
aplicativo acessa — os motores de segurança ficam na rede interna, sem exposição.

### 5. Alertas (camada única, multi-canal)

Um alerta pode sair por **push** (celular), **e-mail** (sempre nos críticos), **webhook**
(integração com assinatura verificável) e **in-app** (central de notificações). Telegram e
SMS estão previstos atrás da mesma camada.

### 6. PWA (a sua tela)

O aplicativo web instalável, mobile-first, por onde você opera: **saúde dos alvos**, **fila
de ocorrências**, **detalhe com timeline** (previsto) e **central de notificações**.

## Motores que o V3RSec adota (por baixo do capo)

O V3RSec adota motores open-source maduros para o trabalho pesado e constrói por cima só o
diferencial (control-plane multi-tenant, detecção de PII/exfiltração, orquestração de
alerta e o PWA):

- **Vector** — coleta, normalização e redação de PII em trânsito.
- **OpenSearch + Security Analytics** — armazenamento, regras Sigma, correlação.
- **CrowdSec** — reputação de IP.
- **Prometheus + Blackbox** — uptime, integridade e certificado (probing externo).
- **MinIO** — armazenamento frio para auditoria.
- **Logto** — identidade e login.

## Princípios que guiam tudo

- **A ferramenta não pode virar vetor.** Ela concentra dados sensíveis, então: menor
  privilégio de coleta, PII redigida antes de persistir, segredos fora do código,
  superfície mínima.
- **Multi-tenant desde o dia 1.** Isolamento é fundação, não remendo.
- **Degradação graciosa.** Teto baixo de um alvo não derruba o valor nos demais.
