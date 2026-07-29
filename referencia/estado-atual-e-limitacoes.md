---
title: Estado atual e limitações
nav_order: 2
parent: Referência
---

# Estado atual e limitacoes

Esta página é o retrato honesto do V3RSec em **28/07/2026**: o que **já funciona**, o que
está **pela metade** e o que **ainda não existe**. O projeto está na fatia de fundação — a
espinha dorsal está de pé, mas a operação ponta a ponta ainda está sendo ligada por partes.

## O que JA funciona

- **Fundação transversal.** Controle de acesso data-driven (RBAC), isolamento por tenant,
  fila assíncrona, logs estruturados sem PII e as migrations do control-plane — tudo
  entregue e validado.
- **Integração de autenticação (código).** O backend valida o token do Logto (OIDC/JWKS) e
  mapeia identidade → tenant → permissões; o PWA tem login/logout via Logto. **Mas** isso só
  autentica de verdade depois do [bootstrap manual do Logto](../admin/bootstrap-logto.md).
- **Coleta do V3RCondo.** O coletor nativo puxa a API de eventos de auditoria do V3RCondo
  de forma incremental, com cursor durável, deduplicação e prova de execução. É a Dep-1,
  entregue.
- **Monitoramento de silêncio (liveness / dead-man's switch).** O V3RSec detecta quando a
  fonte para de emitir o pulso (heartbeat) e sinaliza. É a única detecção real hoje.
- **Segredos fora do código.** Tudo por `.env`/cofre, com permissões explícitas no backend
  Deno.
- **PWA instalável.** O esqueleto do aplicativo é instalável e tem service worker.

## O que esta PELA METADE

- **Telas do PWA.** Saúde dos alvos, fila de ocorrências e central de notificações existem
  em **esqueleto**, com **dados de exemplo (mockados)** para validar layout e
  responsividade. Ainda não mostram dados reais.
- **Ocorrência de liveness.** É detectada, mas fica só em memória — **não é persistida** e
  **some ao reiniciar** o serviço.
- **Trilha de auditoria.** A tabela append-only e os tipos estão prontos; a **captura das
  ações do operador** (reconheceu, tratou, exportou) ainda não foi ligada.
- **Subida via docker compose.** O compose e as configs base estão entregues, mas a
  **validação ponta a ponta** (todos os serviços conversando) ainda está pendente — **o app
  não roda de ponta a ponta ainda**.

## O que AINDA NAO existe

- **Autenticação de verdade em execução.** Depende do [bootstrap do Logto](../admin/bootstrap-logto.md).
  Até lá, ninguém entra.
- **Detecção fina.** As regras Sigma, a correlação e a **detecção própria de
  exfiltração/PII por anomalia de volume** (o diferencial) **não foram implementadas**. Hoje
  só existe a detecção de liveness.
- **Envio real de alertas.** Push, e-mail e webhook ainda não enviam de verdade; a central
  in-app não recebe notificações reais.
- **Ingestão via Vector** de Cloudflare Logpush e webhooks de billing.
- **Uptime/integridade/cert** por probing externo (Prometheus/Blackbox) como detecção ativa.
- **Retenção quente/fria** aplicada (ISM + snapshots) e **exportação de evidência**.
- **Onboarding de alvo automatizado.** Hoje o vínculo alvo → coletor passa por IDs no
  `.env`.
- **Autoatendimento do cliente-operador** (tenant externo em self-service).

## Como ler isto

O V3RSec hoje é uma **fundação sólida com uma trilha de coleta real (V3RCondo) e um único
tipo de detecção (silêncio de fonte)**. As camadas de detecção fina, alerta real e as telas
de operação com dados reais são as próximas fatias. O roadmap detalhado por épico é mantido
no backlog do projeto pela equipe de desenvolvimento.
