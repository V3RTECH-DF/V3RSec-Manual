---
title: Primeiros passos e login
nav_order: 1
parent: Operador de Segurança
---

# Primeiros passos e login

O V3RSec é um aplicativo web instalável (PWA). Você acessa pelo navegador do celular ou
do computador e, no primeiro acesso, pode instalá-lo na tela inicial para receber alertas
por push.

> **Pré-requisito do lado do Admin:** para o login funcionar, o Admin de Plataforma
> precisa ter concluído o [bootstrap do Logto](../admin/bootstrap-logto.md) — criar a
> primeira conta administradora, a organização e o aplicativo de login. **Enquanto esse
> passo não for feito, ninguém consegue entrar.** Se o login não abrir, confira com o
> Admin antes de qualquer outra coisa.

## Como entrar

1. Abra o endereço do V3RSec no navegador (o Admin informa a URL — em ambiente local,
   normalmente `http://localhost:8080`).
2. Você é levado à tela de login do **Logto**, o serviço de identidade do V3RSec.
   `TODO captura: Tela de login do Logto`
3. Informe suas credenciais. O V3RSec não guarda sua senha: quem cuida de login, sessão,
   recuperação e (futuramente) MFA é o Logto.
4. Após entrar, o aplicativo volta para dentro do V3RSec e abre a tela inicial —
   **Saúde dos alvos**.
   `TODO captura: Tela inicial: saúde dos alvos`

## Instalar no celular (para receber push)

O V3RSec é mobile-first e projetado para operação 24h fora do desktop. Para receber
alertas por push, instale o PWA na tela inicial:

- **Android:** o navegador oferece "Adicionar à tela inicial" / "Instalar aplicativo".
- **iPhone (iOS 16.4+):** use **Compartilhar → Adicionar à Tela de Início**. No iOS, o
  push **só** funciona com o PWA adicionado à tela inicial. Por isso, ocorrências
  críticas **sempre também são enviadas por e-mail** — assim você não perde um alerta
  crítico mesmo que o push falhe.

## O que você encontra ao entrar

A navegação principal tem três áreas:

- **Saúde dos alvos** — se cada alvo está sob coleta ativa e quando foi a última coleta.
- **Ocorrências** — a fila de detecções que precisam de atenção.
- **Notificações** — a central de avisos in-app (lido/não-lido).

## Importante sobre o estado atual

Nesta fase do projeto, as telas de operação ainda mostram **dados de exemplo (mockados)**
para validar o layout — a coleta e a detecção reais estão sendo ligadas por partes. Veja
exatamente o que já funciona em [Estado atual e limitações](../referencia/estado-atual-e-limitacoes.md).
