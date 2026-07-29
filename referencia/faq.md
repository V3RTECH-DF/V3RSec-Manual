---
title: Dúvidas frequentes
nav_order: 4
parent: Referência
---

# Dúvidas frequentes (FAQ)

**O V3RSec já está pronto para uso em produção?**
Ainda não. Ele está na fatia de fundação: a espinha dorsal (coleta do V3RCondo,
autenticação integrada, RBAC, multi-tenant) existe, mas detecção fina, envio de alertas e
as telas com dados reais ainda estão sendo construídas. Veja
[Estado atual e limitações](estado-atual-e-limitacoes.md).

**Por que não consigo fazer login?**
Quase sempre porque o [bootstrap do Logto](../admin/bootstrap-logto.md) ainda não foi feito.
Esse passo manual (criar a conta admin, a organização, o app de login e o recurso de API) é
pré-requisito para a autenticação funcionar. Fale com o Admin de Plataforma.

**Preciso de um app da loja (App Store / Play Store)?**
Não. O V3RSec é um **PWA** — um aplicativo web instalável. Você o adiciona à tela inicial
do celular pelo próprio navegador. Um app nativo é uma possibilidade futura, não o MVP.

**Vou receber alertas no celular?**
Essa é a intenção (Web Push). No Android funciona com o PWA instalado; no iPhone (16.4+)
exige o PWA adicionado à tela inicial. Por isso, ocorrências críticas **também** vão por
e-mail. Atenção: o envio real de alertas ainda está sendo implementado — hoje as telas
mostram dados de exemplo.

**Por que uma tela mostra "dados mockados"?**
Porque o layout foi construído antes de a coleta/detecção real estar ligada em todas as
telas. É intencional, para validar a experiência mobile-first cedo. A coleta do V3RCondo já
é real no backend; a exibição na tela está sendo conectada.

**O V3RSec acessa ou altera os dados dos alvos?**
Não. A coleta é **read-only** e de **menor privilégio**: o V3RSec só lê metadados de
eventos de segurança (quem, quando, qual recurso, quanto volume), nunca o conteúdo, e nunca
escreve no alvo. Ele foi desenhado para "não virar vetor" de ataque.

**O que é o "dead-man's switch"?**
É o monitor de silêncio: se um alvo para de emitir sinal por tempo demais, o V3RSec gera
uma ocorrência. Silêncio pode significar que você ficou cego para aquele alvo — melhor ser
avisado. Veja [A fonte V3RCondo](../operador/fonte-v3rcondo.md).

**Por que o V3RSec é "multi-tenant" se só a V3RTECH usa?**
Porque o isolamento por tenant foi construído desde o início (a V3RTECH é o tenant 0). Isso
evita retrabalho caso, no futuro, clientes externos passem a operar os próprios tenants em
autoatendimento.

**Onde ficam os segredos (senhas, tokens)?**
Em variáveis de ambiente (`.env`) / cofre, nunca no código e nunca em logs. O backend Deno
roda com permissões explícitas (menor privilégio).

**O que uso para investigar um incidente a fundo?**
No MVP, a investigação acontece na fila de ocorrências do PWA (em construção). Para análise
avançada existe o OpenSearch Dashboards, que fica na rede interna e é uma ferramenta de
administração — não é o PWA do operador.
