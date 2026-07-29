---
title: Início
nav_order: 1
---

# Manual do V3RSec

Bem-vindo ao manual do **V3RSec**, a ferramenta de monitoramento de segurança e
detecção de vazamento de dados da V3RTECH. Este manual explica, em linguagem simples,
o que a ferramenta faz, quem a usa e como colocá-la para funcionar no estado atual do
projeto.

> **Leitura rápida se você está perdido:** comece por *O que é o V3RSec* (abaixo),
> depois vá para a trilha do seu papel — **Operador de Segurança**, **Admin de
> Plataforma** ou **Cliente-operador** (futuro). Se você só quer entender o que já
> funciona hoje, pule para [Estado atual e limitações](referencia/estado-atual-e-limitacoes.md).

## O que é o V3RSec

O V3RSec observa continuamente os ativos digitais da V3RTECH (como o V3RCondo, o site
institucional e o site de cursos), detecta o que foge do normal em segurança, avisa
quem precisa agir e guarda a evidência do que aconteceu.

Em uma frase: *"A V3RTECH enxerga, em tempo quase real, o que acontece de relevante em
segurança nos seus ativos digitais — e tem prova disso."*

Antes do V3RSec, a checagem de segurança era manual e reativa: alguém olhava os logs
quando desconfiava. O V3RSec troca isso por vigilância automática e contínua, com
alertas acionáveis e trilha auditável — importante inclusive para cumprir prazos
regulatórios e contratuais (por exemplo, notificar um incidente ao parceiro de pagamentos
em até 24 horas, ou à ANPD quando envolver dados pessoais).

O V3RSec é **próprio e independente**: roda na infraestrutura da própria V3RTECH (via
`docker compose`), sem depender de nenhuma plataforma gerenciada de terceiros. É também
**multi-alvo** (observa vários ativos ao mesmo tempo) e **multi-tenant** desde o início
(preparado para, no futuro, atender clientes externos com dados isolados uns dos outros).

## Para quem é (papéis)

| Papel | O que faz | Disponível hoje |
|---|---|---|
| **Operador de Segurança** | Configura os alvos, recebe e trata os alertas, investiga ocorrências e exporta evidência. É o papel do dia a dia. | Sim (papel central do MVP) |
| **Admin de Plataforma** | Cuida da infraestrutura: sobe a ferramenta, gerencia tenants, usuários, papéis e integrações. Fica fora do alcance de qualquer tenant. | Sim (no MVP, uma pessoa acumula os dois papéis) |
| **Cliente-operador** | Futuro: um cliente externo que opera o próprio tenant em autoatendimento. | Ainda não — a fundação já está preparada, mas o autoatendimento é evolução planejada |

> No estágio atual, normalmente **uma única pessoa** (o fundador/técnico da V3RTECH)
> acumula os papéis de Operador e Admin. Ainda assim, a checagem de permissões já passa
> por uma camada única (RBAC), pronta para crescer.

## Como o V3RSec funciona (visão de arquitetura em linguagem de usuário)

Você não precisa entender a engenharia para usar a ferramenta, mas conhecer o caminho
que a informação percorre ajuda a interpretar o que você vê nas telas.

```
Fontes  →  Coletor  →  Armazenamento  →  Backend  →  Aplicativo (PWA)
(alvos)   (puxa/recebe   (guarda e         (decide      (você vê e age:
           os eventos)    indexa)           severidade,   saúde, ocorrências,
                                            agrupa,       notificações)
                                            alerta)
                                              │
                                              └─►  Alertas: push · e-mail · webhook · in-app
```

1. **Fontes (os alvos).** Cada ativo monitorado emite ou expõe sinais de segurança —
   por exemplo, eventos de login, acessos a dados, sinais da borda de rede. Cada alvo
   tem um "teto de observabilidade": o V3RSec só enxerga o que aquele alvo permite
   observar, nunca mais que isso.
2. **Coletor.** Puxa (ou recebe) esses sinais de forma contínua e agendada, com prova de
   que a coleta aconteceu. Se uma fonte fica em silêncio, isso vira um sinal de alerta —
   silêncio pode significar cegueira, não "está tudo bem".
3. **Armazenamento (OpenSearch).** Guarda e indexa os eventos, com retenção quente (90
   dias, consultável) e fria (até 1 ano, para auditoria).
4. **Backend.** É o cérebro: recebe as detecções, define a severidade, agrupa eventos
   repetidos para não gerar ruído, cria a **ocorrência** e dispara os alertas.
5. **Alertas.** Chegam por vários canais: **push** no celular, **e-mail**, **webhook**
   (para integrar com outros sistemas) e **notificação in-app** dentro do próprio
   aplicativo. Ocorrências críticas sempre também vão por e-mail.
6. **Aplicativo (PWA).** É a tela por onde você opera: um aplicativo web instalável no
   celular (mobile-first) onde você vê a saúde dos alvos, a fila de ocorrências e a
   central de notificações.

Para o detalhe em linguagem de usuário, veja [Referência: arquitetura](referencia/arquitetura.md).

## Índice do manual

- **Operador de Segurança**
  - [Primeiros passos e login](operador/primeiros-passos.md)
  - [Ver a saúde dos alvos](operador/saude-dos-alvos.md)
  - [Acompanhar a fila de ocorrências](operador/fila-de-ocorrencias.md)
  - [Usar a central de notificações](operador/central-de-notificacoes.md)
  - [A fonte V3RCondo: o que é coletado](operador/fonte-v3rcondo.md)
- **Admin de Plataforma**
  - [Colocar o V3RSec de pé (docker compose)](admin/colocar-de-pe.md)
  - [Bootstrap do Logto (passo manual obrigatório)](admin/bootstrap-logto.md)
- **Referência**
  - [Como o V3RSec funciona (arquitetura)](referencia/arquitetura.md)
  - [Estado atual e limitações](referencia/estado-atual-e-limitacoes.md)
  - [Novidades](referencia/novidades.md)
  - [Dúvidas frequentes (FAQ)](referencia/faq.md)
  - [Privacidade e Termos](referencia/privacidade-e-termos.md)
