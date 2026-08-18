# Roteiro de capturas de tela (prints)

As imagens abaixo são referenciadas pelas páginas do manual, mas **ainda não foram
capturadas** — o app não roda de ponta a ponta nesta fatia. Quando o V3RSec subir por
completo (após o [bootstrap do Logto](../admin/bootstrap-logto.md) e a validação da subida),
capture cada tela com um **usuário de teste/demo** (nunca com dados pessoais reais) e salve
o PNG **nesta pasta** com exatamente o nome indicado.

Viewport preferencial: **desktop 1920x1080**; onde indicado, também **mobile 375px**.

| Arquivo | Tela / rota | Viewport | O que precisa aparecer |
|---|---|---|---|
| `login-01.png` | Login do Logto (`/` → redireciona para o Logto) | desktop | Tela de login do Logto antes de autenticar. |
| `saude-alvos-01.png` | Saúde dos alvos (`/`) | desktop | Lista de alvos com nome, última coleta e selos de status (Ativo / Em silêncio / Coleta pendente). |
| `saude-alvos-02.png` | Saúde dos alvos (`/`) | mobile (375px) | Mesma tela adaptada ao celular (validação mobile-first). |
| `fila-ocorrencias-01.png` | Fila de ocorrências (`/ocorrencias`) | desktop | Fila com itens de ocorrência (severidade, classe, status) e filtros. |
| `central-notificacoes-01.png` | Central de notificações (`/notificacoes`) | desktop | Lista de avisos com estado lido/não-lido. |
| `bootstrap-logto-01.png` | Console admin do Logto (`http://admin.localhost:3302`), primeiro acesso | desktop | Criação da primeira conta admin do Logto. **Pendente** — só existe no primeiro acesso a uma instância nova; reproduzir exige zerar o Logto atual (ver nota na própria página). |
| `bootstrap-logto-02.png` ✅ | `/console/organizations/or2ivomanag5/settings` | desktop | Capturado em 18/08/2026, v0.1.0. Configurações da organização V3RTECH, Nome e Descrição preenchidos. |
| `bootstrap-logto-03.png` ✅ | `/console/organization-template/organization-roles` | desktop | Capturado em 18/08/2026, v0.1.0. Lista com os três papéis de organização (`admin_plataforma`, `operador_seguranca`, `visualizador`). |
| `bootstrap-logto-04.png` ✅ | `/console/users/<id>/settings` | desktop | Capturado em 18/08/2026, v0.1.0. Perfil do usuário criado para o V3RSec, com e-mail visível (dono do ambiente, captura autorizada). |
| `bootstrap-logto-05.png` ✅ | `/console/organizations/or2ivomanag5/members` | desktop | Capturado em 18/08/2026, v0.1.0. Membro da organização com o menu de ações aberto (Edit organization roles). |
| `bootstrap-logto-06.png` ✅ | `/console/applications` | desktop | Capturado em 18/08/2026, v0.1.0. Lista de aplicativos com o V3RSec PWA, tipo Single Page App. |
| `bootstrap-logto-07.png` ✅ | `/console/applications/<id>/settings` | desktop | Capturado em 18/08/2026, v0.1.0. Redirect URIs, post-sign-out e CORS allowed origins preenchidos (porta 8081). |
| `bootstrap-logto-08.png` ✅ | `/console/api-resources/<id>/settings` | desktop | Capturado em 18/08/2026, v0.1.0. Identificador `urn:v3rsec:api` preenchido. |

Capturas ✅ feitas com o console já configurado nesta instância (organização, papéis, app e
API resource já existiam de uma sessão de bootstrap anterior) — por isso ilustram o
**resultado** de cada passo de criação, não o diálogo de criação vazio em si. Nenhuma tela
teve dado pessoal de terceiro exposto; o único e-mail visível (`bootstrap-logto-04.png`) é
do dono do próprio ambiente, autorizado pelo enunciado da tarefa.

## Observações

- A skill **nunca faz login** e não captura estas telas automaticamente. Quem autentica e
  captura é o usuário/operador.
- Evite timestamps e dados chamativos nas capturas; se um print sair ruim, basta refazer.
- Enquanto os PNGs não existirem, o site MkDocs mostrará imagens quebradas nessas páginas —
  é o comportamento esperado do roteiro de prints até a captura acontecer.
