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
| `bootstrap-logto-01.png` | Console admin do Logto (`http://localhost:3302`) | desktop | Passo de criação da primeira conta admin. |
| `bootstrap-logto-02.png` | Console admin do Logto | desktop | Criação da organização (tenant V3RTECH). |
| `bootstrap-logto-03.png` | Console admin do Logto | desktop | Registro do API Resource (`urn:v3rsec:api`). |

## Observações

- A skill **nunca faz login** e não captura estas telas automaticamente. Quem autentica e
  captura é o usuário/operador.
- Evite timestamps e dados chamativos nas capturas; se um print sair ruim, basta refazer.
- Enquanto os PNGs não existirem, o site MkDocs mostrará imagens quebradas nessas páginas —
  é o comportamento esperado do roteiro de prints até a captura acontecer.
