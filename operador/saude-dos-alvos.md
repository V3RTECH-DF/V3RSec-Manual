---
title: Ver a saúde dos alvos
nav_order: 2
parent: Operador de Segurança
---

# Ver a saúde dos alvos

A tela **Saúde dos alvos** é a primeira que você vê ao entrar. Ela responde a uma
pergunta simples e crítica: *estou realmente enxergando cada alvo agora?*

## O que a tela mostra

Para cada alvo monitorado, você vê:

- **Nome do alvo** (ex.: "V3RCondo — produção").
- **Última coleta** — quando o V3RSec recebeu sinal daquele alvo pela última vez.
- **Status** — um selo colorido indicando a situação da coleta.

![Saúde dos alvos no desktop](../assets/saude-alvos-01.png)

## Os status possíveis

| Status | O que significa |
|---|---|
| **Ativo** | O alvo está sob coleta contínua e enviou sinal recentemente. Tudo certo. |
| **Em silêncio** | A fonte parou de emitir sinais. **Isso é um alerta**, não um "está tudo bem" — silêncio pode significar que você ficou cego para aquele alvo. |
| **Coleta pendente** | O alvo está cadastrado, mas a coleta ainda não começou. |
| **Erro** | A coleta falhou. Precisa de atenção. |

## Por que "silêncio" é importante: o dead-man's switch

O V3RSec trata a **ausência de sinal** como um evento de segurança. Se um alvo que deveria
estar mandando eventos fica quieto tempo demais, o sistema gera uma ocorrência de saúde —
o chamado *dead-man's switch* ("interruptor de homem morto"). A lógica é: é melhor ser
avisado de que a vigilância caiu do que descobrir isso só depois de um incidente.

## No celular

A mesma tela se adapta ao celular (mobile-first), para você conferir a saúde dos alvos em
trânsito.

![Saúde dos alvos no celular](../assets/saude-alvos-02.png)

## Estado atual desta tela

Hoje esta tela exibe **dados de exemplo** para validar o layout. A coleta real do primeiro
alvo (V3RCondo) já está implementada no backend, e o monitoramento de silêncio (liveness)
também — mas a ligação dessa informação real na tela e a persistência das ocorrências de
liveness ainda estão em andamento. Detalhe em
[Estado atual e limitações](../referencia/estado-atual-e-limitacoes.md).
