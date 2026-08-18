---
title: Novidades
nav_order: 3
parent: Referência
---

# Novidades

Resumo curado, em linguagem leve, do que foi entregue recentemente no V3RSec.

## Agosto de 2026 — login de verdade e primeira coleta persistida

- **Login funcionando de ponta a ponta.** O que em julho era só integração de código agora
  funciona na prática: com o bootstrap do serviço de identidade feito, o Operador de
  Segurança e o Admin de Plataforma já entram no V3RSec com usuário e senha reais, cada um
  vendo só o que o papel dele permite.
- **A coleta do V3RCondo passou a gravar de verdade.** Não é mais só "o coletor roda sem
  erro": os eventos de auditoria do V3RCondo estão persistidos no armazenamento do V3RSec —
  mais de 6.200 eventos reais já coletados e guardados, sem duplicar e sem perder o que já
  tinha sido puxado antes.
- **Trilha de auditoria própria.** O V3RSec agora tem uma trilha interna que só cresce —
  registro que não se altera nem se apaga depois de escrito.

> Quer saber exatamente o que já funciona e o que ainda está por vir? Veja
> [Estado atual e limitações](estado-atual-e-limitacoes.md).

## Julho de 2026 — fatia de fundação

- **Base pronta para crescer.** Entramos com a fundação da ferramenta: controle de acesso
  por papéis (RBAC), isolamento por tenant, fila de tarefas assíncronas, logs sem dados
  sensíveis e a estrutura do banco de metadados.
- **Login via Logto (integração de código).** O V3RSec passou a falar com o Logto para
  autenticação — falta apenas o passo manual de configuração inicial para o login entrar em
  operação.
- **Primeira coleta real: V3RCondo.** O V3RSec já puxa os eventos de auditoria do V3RCondo
  de forma contínua, sem clique manual, sem repetir eventos e com prova de que a coleta
  aconteceu.
- **Alerta de silêncio (dead-man's switch).** Se um alvo para de dar sinal de vida, o
  V3RSec percebe — porque silêncio pode ser cegueira, não calmaria.
- **Aplicativo instalável.** O esqueleto do PWA já é instalável no celular, com as três
  áreas principais (saúde dos alvos, ocorrências, notificações) desenhadas.
