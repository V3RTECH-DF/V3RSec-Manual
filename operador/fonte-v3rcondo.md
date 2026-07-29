---
title: A fonte V3RCondo
nav_order: 5
parent: Operador de Segurança
---

# A fonte V3RCondo: o que é coletado

O **V3RCondo** é o primeiro alvo do V3RSec e o caso de referência da coleta. Como a
V3RTECH não controla a infraestrutura do V3RCondo (ele roda em Supabase gerenciado pelo
Lovable), o V3RSec opera no teto de observabilidade "só logs": ele **nunca escreve** no
V3RCondo, apenas **lê** eventos de auditoria que o próprio V3RCondo expõe.

## Como a coleta funciona

O V3RCondo expõe uma **API de eventos de auditoria** (Audit Events API). Um coletor
nativo do V3RSec (um worker dedicado) **puxa** esses eventos de forma incremental e
contínua:

- usa um **cursor durável** para lembrar até onde já leu (não relê o que já processou);
- **deduplica** por identificador de evento (o mesmo evento nunca conta duas vezes);
- é resiliente a limites de taxa e atrasos (backoff/retentativa);
- registra **prova de execução**: a última coleta bem-sucedida fica gravada e observável.

O acesso é read-only e por token, guardado apenas no cofre de segredos do V3RSec (nunca
versionado, nunca registrado em log).

## Que eventos o V3RSec ingere

Os eventos de auditoria do V3RCondo cobrem, entre outros:

- **Autenticação** (`auth`) — eventos de login e sessão.
- **Acesso a dados** (`data_access`) — leituras/listagens/exportações, **com `row_count`**
  (quantas linhas foram retornadas). Esse volume é o insumo do diferencial do V3RSec:
  detectar exfiltração por anomalia de volume ("alguém exportou 5.000 registros de
  moradores").
- **Administração** (`admin`) — ações administrativas.
- **Cobrança** (`billing`) — sinais de billing.
- **Sistema / heartbeat** (`system`) — o "pulso" que prova que a fonte está viva.
- **Segurança** (`security`) — eventos de segurança do próprio app.

> O V3RSec precisa apenas dos **metadados** (quem, quando, qual recurso, quanto volume) —
> **não** do conteúdo dos dados.

## O dead-man's switch (silêncio = incidente)

O tipo de evento **heartbeat** alimenta um monitor de vitalidade: se o V3RCondo parar de
emitir o pulso por tempo demais, o V3RSec entende que **perdeu a visão** do alvo e gera uma
ocorrência de saúde. Ausência de sinal é tratada como problema, não como calmaria. Você vê
o reflexo disso no status **Em silêncio** da tela [Saúde dos alvos](saude-dos-alvos.md).

## Pontos cegos declarados (o que o V3RSec ainda NÃO enxerga no V3RCondo)

Honestidade é regra do projeto: o V3RSec só promete detectar o que o alvo permite observar.
No V3RCondo, ficam declarados como pontos cegos, entre outros:

- **`login.failure`**, **`logout`** e **`password.changed`** — se o V3RCondo ainda não
  emite esses eventos de auth, o V3RSec não os vê (limita a detecção de força bruta e
  troca de senha suspeita).
- **Consulta SQL crua (`SELECT`)** feita fora da instrumentação do app — se um acesso a
  dados não passa pela camada que registra `row_count`, ele **não** aparece, e essa classe
  fica cega para aquele caminho.

Esses pontos cegos são assumidos de propósito, para não "vender" uma detecção que o teto de
observabilidade do alvo não sustenta. Ampliá-los depende de o V3RCondo instrumentar mais
eventos na origem.
