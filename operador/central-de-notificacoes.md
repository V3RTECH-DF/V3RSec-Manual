---
title: Central de notificações
nav_order: 4
parent: Operador de Segurança
---

# Usar a central de notificações

A **central de notificações** é o canal *in-app* de alertas: os avisos aparecem dentro do
próprio aplicativo, com controle de lido/não-lido e link direto para a ocorrência
relacionada. É um dos quatro canais de alerta do V3RSec.

## Os quatro canais de alerta

Todo alerta passa por uma **camada única de notificação** que o entrega por, até, quatro
canais:

- **Push** — no celular, com o PWA instalado. É o canal principal da operação 24h.
- **E-mail** — sempre acionado para ocorrências críticas (garante o aviso mesmo se o push
  falhar, por exemplo no iOS sem o PWA instalado).
- **Webhook** — para integrar o alerta a outro sistema (com assinatura verificável).
- **In-app** — esta central de notificações.

> Telegram e SMS estão *previstos* atrás da mesma camada, mas não fazem parte do MVP.

## O que você faz aqui

- Ver os avisos recebidos, com estado **lido / não-lido**.
- Clicar no aviso para abrir a **ocorrência** correspondente e investigar.

`TODO captura: Central de notificações`

## Anti-flood: um incidente não vira cem avisos

Para não afogar você em alertas, o V3RSec agrupa e limita a frequência: um mesmo incidente
gera um alerta consolidado, não uma enxurrada de mensagens. A ideia é que todo alerta seja
**acionável** — quando chega, é para você olhar.

## Estado atual desta tela

Tela em **esqueleto**. O envio real de alertas (push, e-mail, webhook) e a alimentação da
central com notificações reais são fatias ainda **não implementadas** (épico de alertas).
O que existe hoje é a forma da tela. Veja
[Estado atual e limitações](../referencia/estado-atual-e-limitacoes.md).
