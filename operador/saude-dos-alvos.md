---
title: Ver a saúde dos alvos
nav_order: 2
parent: Operador de Segurança
---

# Ver a saúde dos alvos

## Por que isto importa

O V3RSec só te protege do que ele consegue **ver**. Se a coleta de um alvo para e ninguém
percebe, você passa a operar cego naquele ativo — e um incidente pode acontecer exatamente
na janela em que a vigilância caiu, sem gerar nenhum alerta, porque não há detecção sem
sinal chegando. **Saúde dos alvos** existe para responder, em poucos segundos, uma pergunta
que nenhuma outra tela responde: *estou realmente enxergando cada alvo agora, ou só acho que
estou?*

É por isso que esta é a primeira tela que você vê ao entrar: antes de olhar qualquer
ocorrência, vale confirmar que a vigilância que sustenta essas ocorrências está de pé.

## O que a tela mostra

Para cada alvo monitorado, você vê:

- **Nome do alvo** (ex.: "V3RCondo — produção").
- **Última coleta** — quando o V3RSec recebeu sinal daquele alvo pela última vez.
- **Status** — um selo colorido indicando a situação da coleta.

![Saúde dos alvos no desktop](../assets/saude-alvos-01.png)

## Como conferir a saúde de um alvo

1. Entre no V3RSec — a tela **Saúde dos alvos** já abre por padrão.
2. Percorra a lista e localize o alvo que te interessa.
3. Olhe o selo de **Status** primeiro, não a data — o selo já traduz "última coleta" em
   "isso é normal ou não".
4. Se o selo não for **Ativo**, abra o alvo para ver o horário exato da última coleta e
   decidir a próxima ação (ver [Dicas e armadilhas](#dicas-e-armadilhas), abaixo).

## Exemplo concreto

Às 14h32 de uma terça-feira, você abre a tela e vê:

| Alvo | Última coleta | Status |
|---|---|---|
| V3RCondo — produção | 14h29 (3 min atrás) | **Ativo** |
| Site institucional | 09h10 (5h22 atrás) | **Em silêncio** |
| Site de cursos | — | **Coleta pendente** |

O **V3RCondo** está normal: 3 minutos de defasagem é esperado para o intervalo de coleta
configurado. O **Site institucional** já é motivo de atenção — mais de 5 horas sem sinal
para um alvo que deveria emitir com frequência é exatamente o cenário que o
*dead-man's switch* existe para capturar. O **Site de cursos** ainda não tem coleta ligada:
é esperado enquanto ele não for cadastrado por completo, não é falha.

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

## Dicas e armadilhas

- **Não ignore "Em silêncio" achando que é só atraso.** O intervalo de coleta é curto
  (minutos); um alvo que deveria falar e não fala por horas não é "lentidão", é perda de
  visibilidade — trate como incidente de saúde da própria ferramenta, não como ruído.
- **"Coleta pendente" não é o mesmo que "Erro".** Pendente é esperado para um alvo recém
  cadastrado; erro é uma falha ativa que precisa de investigação. Confundir os dois te faz
  ou ignorar uma falha real, ou perseguir um alvo que só está no meio do cadastro.
- **Um alvo "Ativo" não garante detecção.** Coleta funcionando quer dizer que o V3RSec está
  recebendo sinal — não que toda classe de ameaça já está sendo detectada sobre esse sinal.
  Confira o que já está coberto em
  [Estado atual e limitações](../referencia/estado-atual-e-limitacoes.md) antes de assumir
  cobertura total.

## Quando dá errado

- **Um alvo aparece como "Erro".** A causa mais comum é a credencial de coleta ter expirado
  ou mudado do lado do alvo — a chave de acesso à auditoria do V3RCondo, por exemplo, é
  obrigatória para o coletor iniciar. Acione o Admin de Plataforma para confirmar se ela
  continua válida; a chave nunca aparece em tela nem em registro, por segurança.
- **Todos os alvos aparecem "Em silêncio" ao mesmo tempo.** Isso aponta para a própria
  infraestrutura do V3RSec, não para os alvos: o processo que faz a coleta ou a fila que o
  alimenta podem estar parados. Acione o Admin de Plataforma em vez de investigar alvo por
  alvo — o sintoma é coletivo porque a causa é única.
- **A tela não carrega nenhum alvo.** Confira se o login foi concluído e se o
  [bootstrap do Logto](../admin/bootstrap-logto.md) já foi feito; sem ele a autenticação não
  completa e nenhum dado do tenant é exibido.

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

> **Sobre o exemplo acima:** enquanto a tela mostra dados de exemplo, os horários e status do
> quadro em "Exemplo concreto" são ilustrativos. A mecânica de leitura — olhar o selo antes
> da data — é a mesma que vale quando os dados forem reais.
