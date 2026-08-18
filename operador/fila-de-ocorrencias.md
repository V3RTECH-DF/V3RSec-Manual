---
title: Fila de ocorrências
nav_order: 3
parent: Operador de Segurança
---

# Acompanhar a fila de ocorrências

Uma **ocorrência** é o que o V3RSec cria quando detecta algo que merece sua atenção. Ela
reúne, num único item, a detecção, sua severidade, a classe de ameaça e a evidência (os
eventos que a originaram). A tela **Ocorrências** é a sua fila de trabalho.

## O que é uma ocorrência

Cada ocorrência carrega:

- **Severidade** — quão urgente é.
- **Classe de ameaça** — a que tipo de risco pertence:
  - **Acesso anômalo** (logins fora de padrão, força bruta, escalada de privilégio);
  - **Exfiltração / vazamento de PII** (volume anômalo de leitura/exportação de dados);
  - **Tentativa de intrusão** (scans, injeção, exploração);
  - **Integridade & uptime** (indisponibilidade, defacement, expiração de certificado).
- **Evidência vinculada** — os eventos de origem que sustentam a detecção.
- **Status** — nova, em análise, tratada ou falso-positivo.

![Fila de ocorrências](/assets/fila-ocorrencias-01.png)

## Como você trabalha a fila

O fluxo previsto é filtrar por alvo, tenant, classe, severidade e status, abrir a
ocorrência, analisar a evidência e a linha do tempo dos eventos correlacionados, e então
marcar como tratada — ou como **falso-positivo**, o que ajuda a afinar as regras e reduzir
ruído ao longo do tempo. Toda ação sua fica registrada na trilha de auditoria.

## Estado atual desta tela

Esta é uma das telas em **esqueleto**: a forma e os filtros estão sendo montados, mas a
detecção que alimenta a fila com ocorrências reais (regras Sigma, correlação, e a detecção
própria de exfiltração/PII) ainda **não foi implementada**. Por enquanto, a única fonte de
ocorrência real é o monitoramento de silêncio de fonte (liveness) — e essa ocorrência
ainda não é persistida entre reinícios. Veja
[Estado atual e limitações](../referencia/estado-atual-e-limitacoes.md).
