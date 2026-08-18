---
title: Estado atual e limitações
nav_order: 2
parent: Referência
---

# Estado atual e limitacoes

Esta página é o retrato honesto do V3RSec em **18/08/2026**: o que **já funciona**, o que
está **pela metade** e o que **ainda não existe**. O projeto está na fatia de fundação — a
espinha dorsal está de pé e a primeira coleta já grava de verdade, mas a operação ponta a
ponta (ver o que aconteceu e ser avisado) ainda está sendo ligada por partes.

> **Por que esta página existe, e por que ler antes de confiar no painel.** As telas do PWA
> hoje mostram dados de exemplo, não dados reais — e nada nelas avisa isso na cara. Sem ler
> esta página, dá para olhar o painel e achar que o ambiente monitorado está sendo vigiado
> de fato, quando ainda não está. Volte aqui sempre que tiver dúvida sobre o que uma tela
> realmente representa.

## O que JA funciona

- **Subida da stack via docker compose.** Com os certificados do OpenSearch gerados e o
  `.env` preenchido, `docker compose up -d --build` sobe todos os serviços e as migrations
  do control-plane rodam sozinhas — veja [Colocar o V3RSec de pé](../admin/colocar-de-pe.md).
- **Autenticação em execução.** Depois do [bootstrap do Logto](../admin/bootstrap-logto.md)
  (passo manual, único por instalação), o login funciona de ponta a ponta: o backend valida
  o token, mapeia identidade → tenant → permissões, e o Operador de Segurança e o Admin de
  Plataforma entram de verdade, cada um vendo só o que o papel dele permite.
- **Coleta do V3RCondo, persistindo de fato.** O coletor nativo puxa a API de eventos de
  auditoria do V3RCondo de forma incremental (cursor durável, deduplicação, prova de
  execução) **e os eventos ficam gravados** no armazenamento do V3RSec — não é mais só "o
  coletor roda sem erro". É a Dep-1, entregue e validada em execução.
- **Fundação transversal.** Controle de acesso data-driven (RBAC), isolamento por tenant,
  fila assíncrona, logs estruturados sem PII — tudo entregue e validado.
- **Monitoramento de silêncio (liveness / dead-man's switch).** O V3RSec detecta quando a
  fonte para de emitir o pulso (heartbeat) e sinaliza. É a única detecção real hoje — ver
  abaixo por que "detecção real" não é o mesmo que "detecção fina".
- **Trilha de auditoria (append-only).** O mecanismo funciona: uma vez escrito, nenhum
  registro pode ser alterado ou apagado. A cobertura ainda está subindo — nem toda ação do
  operador (reconheceu, tratou, exportou uma ocorrência) grava um registro ainda.
- **Segredos fora do código.** Tudo por `.env`/cofre, com permissões explícitas no backend
  Deno.
- **PWA instalável.** O esqueleto do aplicativo é instalável e tem service worker.

## O que esta PELA METADE

- **Telas do PWA.** Saúde dos alvos, fila de ocorrências e central de notificações existem
  em **esqueleto**, com **dados de exemplo (mockados)** para validar layout e
  responsividade. A coleta do V3RCondo já é real e já grava — mas essas telas **ainda não
  mostram esses dados reais**, mostram exemplo. Não confunda "a coleta funciona" com "a
  tela mostra o que foi coletado": são coisas diferentes, e é aqui que mora o risco de achar
  que há vigilância onde ainda não há.
- **Ocorrência de liveness.** É detectada, mas fica só em memória — **não é persistida** e
  **some ao reiniciar** o serviço.
- **Trilha de auditoria — cobertura de ações do operador.** A estrutura append-only já
  funciona (ver acima); falta ligar a captura das ações específicas do operador na
  interface.

## O que AINDA NAO existe

- **Detecção fina.** As regras Sigma, a correlação e a **detecção própria de
  exfiltração/PII por anomalia de volume** (o diferencial) **não foram implementadas**. Hoje
  só existe a detecção de liveness (silêncio de fonte) — nenhuma regra roda sobre o conteúdo
  dos eventos coletados.
- **Envio real de alertas.** Nenhuma notificação sai de verdade hoje — nem push, nem
  e-mail, nem webhook. A central in-app do PWA não recebe notificação real; o que aparece
  nela é exemplo.
- **Ingestão via Vector** de Cloudflare Logpush e webhooks de billing.
- **Uptime/integridade/cert** por probing externo (Prometheus/Blackbox) como detecção ativa.
- **Retenção quente/fria** aplicada (ISM + snapshots) e **exportação de evidência**.
- **Onboarding de alvo automatizado.** Hoje o vínculo alvo → coletor passa por IDs no
  `.env`.
- **Autoatendimento do cliente-operador** (tenant externo em self-service).

## Defeito conhecido

- **Laço de requisições no PWA (issue #45).** Em determinadas condições o PWA repete
  chamadas ao backend sem necessidade, consumindo recurso do navegador à toa. Não expõe
  dado nem derruba o backend, mas é um defeito real, não um comportamento esperado — segue
  aberto no rastreador do projeto.

## Como ler isto

O V3RSec hoje **autentica de verdade, sobe de ponta a ponta e já persiste uma coleta real**
(V3RCondo) com trilha de auditoria funcionando. O que falta para virar vigilância de fato é
o que o produto promete de diferencial: **ler** o que foi coletado (detecção fina) e
**avisar** alguém quando algo importa (alertas reais) — hoje as telas de operação ainda
mostram dados de exemplo, não o resultado da coleta real. O roadmap detalhado por épico é
mantido no backlog do projeto pela equipe de desenvolvimento.
