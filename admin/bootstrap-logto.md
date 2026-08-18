---
title: Bootstrap do Logto
nav_order: 2
parent: Admin de Plataforma
---

# Bootstrap do Logto (passo manual obrigatorio)

O V3RSec usa o **Logto** como serviço de identidade (login, sessão, recuperação, futura
MFA). A integração no código já está pronta, mas o Logto começa "vazio": ele precisa de uma
configuração inicial feita **uma única vez, manualmente**, no console de administração.
Enquanto isso não for feito, **o V3RSec não autentica ninguém** — o login não passa.

> Este é um passo de **credencial/configuração**, não de código. É o item `FND-AUTHN-SETUP`
> do backlog, marcado como pendente (manual).

Este roteiro foi validado ponta a ponta em execução real (18/08/2026). Ele existe porque a
primeira execução travou em três pontos diferentes — dois deles **sem nenhuma mensagem de
erro**. Siga a ordem: pular um passo não quebra na hora, quebra silenciosamente lá na frente.

## Antes de começar

- O V3RSec (incluindo o container do Logto) precisa já estar no ar — veja
  [Colocar o V3RSec de pe](colocar-de-pe.md).
- Você vai acessar o **console admin do Logto**.

> **Atenção — o endereço não é `http://localhost:3302`.** É **`http://admin.localhost:3302`**
> (variável `LOGTO_ADMIN_ENDPOINT`). Com `localhost` puro a tela carrega normalmente, mas
> **nenhum botão de criação funciona** — sem mensagem de erro nenhuma, o clique simplesmente
> não faz nada. O motivo: o Logto, rodando em modo produção, recusa por segurança qualquer
> requisição vinda de uma origem cujo hostname é exatamente `localhost`. Usar
> `admin.localhost` resolve porque o navegador resolve sozinho qualquer `*.localhost` para
> `127.0.0.1`, sem precisar editar `/etc/hosts`. Em produção, com domínio próprio, esse
> problema não existe — ele é só do ambiente local.

## Conceito — duas contas, dois "mundos"

Antes do passo a passo, uma distinção que a interface do Logto não deixa óbvia e que é a
maior fonte de confusão deste bootstrap: **o Logto mantém dois tenants internos**, e a conta
que você usa para administrar o console **não é** a conta com que alguém faz login no
V3RSec.

- **Tenant `admin`** — onde vive a conta criada no primeiro acesso ao console. Ela só serve
  para administrar o próprio Logto (criar organizações, aplicativos, papéis). Não é usuário
  do V3RSec.
- **Tenant `default`** — onde vivem os usuários **das aplicações**, isto é, as pessoas que
  vão logar no V3RSec. É criado em **User management**, dentro do console.

Se você tentar adicionar a conta de administrador como membro de uma organização, a busca
retorna **"No data"** sem explicação — porque ela está no tenant errado para aquilo. Trate
sempre como duas contas diferentes.

## Passo a passo

### 1. Criar a primeira conta administradora do Logto

No primeiro acesso ao console admin (`http://admin.localhost:3302`), ele pede para você
**criar a conta de administrador** do próprio Logto. Essa conta gerencia o serviço de
identidade — ela não é, e nunca vai aparecer como, usuário do V3RSec.

> **Captura pendente.** Esta tela só aparece no **primeiro acesso** ao console de um Logto
> recém-instalado, antes de qualquer conta existir. Neste ambiente esse passo já tinha
> acontecido antes desta sessão de captura — reproduzi-lo exigiria zerar a instância do
> Logto (perder organização, papéis e aplicativo já configurados), o que não faz sentido só
> para o print. Fica como pendência para a próxima instalação nova (ex.: ambiente de outro
> desenvolvedor ou de produção).

### 2. Criar a organização (= tenant V3RTECH)

No V3RSec, **uma organização do Logto corresponde a um tenant**. Crie a organização da
**V3RTECH** (o tenant 0). É ela que, no futuro, permite isolar clientes externos em tenants
próprios.

![Tela de configurações da organização V3RTECH no console do Logto, com os campos Nome e Descrição preenchidos](/assets/bootstrap-logto-02.png)

Anote o **Organization ID** que o Logto gera — você vai precisar dele no passo 6. Neste
ambiente ele saiu como `or2ivomanag5`; o seu vai ser outro, gerado a cada instalação.

### 3. Criar os três papéis de organização

Dentro da organização, crie os papéis de organização com **estes três nomes, literais**:

- `operador_seguranca`
- `admin_plataforma`
- `visualizador`

![Lista de papéis de organização no console do Logto mostrando os três papéis criados: admin_plataforma, operador_seguranca e visualizador](/assets/bootstrap-logto-03.png)

> **Atenção — o nome tem que bater exatamente.** O V3RSec lê o nome do papel como claim do
> token e casa com uma tabela de mapeamento no banco (`idp_role_mappings`). Nome divergente
> — maiúscula diferente, espaço, plural — não dá erro nenhum: o usuário loga normalmente e o
> V3RSec simplesmente não reconhece nenhum papel para ele, como se não pudesse fazer nada.

Ao criar cada papel, o Logto abre um diálogo **"Assign permissions"**. Responda **Skip**. No
V3RSec a autorização é in-app — o RBAC completo (quem pode ver o quê, quem pode agir onde)
vive no backend, não no Logto. O papel de organização serve só como claim que chega no
token; atribuir permissões aqui dentro não tem efeito nenhum no V3RSec e não corresponde a
nada que o produto use.

### 4. Criar o usuário do V3RSec

Ainda no console, vá em **User management** e crie o usuário que vai logar no V3RSec (o
e-mail e a senha da pessoa que vai operar a plataforma). Este é o usuário do tenant
`default` — o que importa para o produto, diferente da conta do passo 1.

![Tela de detalhes do usuário no console do Logto, com o perfil mostrando nome e e-mail preenchidos](/assets/bootstrap-logto-04.png)

### 5. Adicionar o usuário à organização e atribuir os papéis

São **dois passos distintos** na interface — fazer só o primeiro deixa o login funcionando
sem que a pessoa possa fazer nada, e não há aviso disso em lugar nenhum:

1. **Organizations → a organização V3RTECH → Members → Add member.** Busque pelo e-mail do
   usuário criado no passo 4 e adicione-o.
2. No menu de ações da linha do membro, abra **"Edit organization roles"** e marque o(s)
   papel(is) que essa pessoa deve ter (`operador_seguranca`, `admin_plataforma` ou
   `visualizador`).

![Tela de membros da organização V3RTECH no console do Logto, com o menu de ações do membro aberto mostrando as opções de edição de papéis](/assets/bootstrap-logto-05.png)

### 6. Vincular a organização ao tenant do V3RSec (banco de dados)

Este passo **não tem tela** — é um `INSERT` direto no banco do V3RSec, e sem ele o login
passa no Logto mas o V3RSec devolve **401** sem explicar o motivo.

Você precisa de dois valores:

- O **Organization ID** do Logto, anotado no passo 2 (ou visível na URL/detalhes da
  organização no console).
- O **id do tenant** do V3RSec correspondente, que você obtém pelo `slug` — o tenant da
  V3RTECH tem `slug = 'v3rtech'`.

```sql
INSERT INTO idp_organizations (org_id, tenant_id)
SELECT '<org-id-do-logto>', id FROM tenants WHERE slug = 'v3rtech';
```

Rode isso no Postgres de control-plane do V3RSec (não no Postgres do Logto — são bancos
diferentes). É essa linha que o backend consulta para saber a qual tenant cada organização
do token pertence.

### 7. Criar o aplicativo de login (SPA) para o PWA

Crie um **aplicativo do tipo SPA** (single-page application) para o PWA do V3RSec. O Logto
gera um **App ID** — copie-o para a variável `VITE_LOGTO_APP_ID` no seu `.env`. (Apps SPA do
Logto não usam client secret; o App ID é público.) Neste ambiente o App ID saiu como
`5hhp30h3qztxjhvtsrph6`; o seu vai ser outro.

![Lista de aplicativos no console do Logto mostrando o aplicativo V3RSec PWA cadastrado como Single Page App](/assets/bootstrap-logto-06.png)

> **A porta do redirect é a porta em que o PWA foi publicado, não uma porta fixa.** O código
> monta a URL de redirect a partir da origem do navegador — se o PWA subiu em `8081`, o
> redirect é `http://localhost:8081/callback` e o pós-logout é `http://localhost:8081`.
> Atenção: o Docker **não** troca de porta sozinho. Se a porta configurada já estiver ocupada
> no host, ele falha a subida com `port is already allocated` e você precisa ajustar
> `PWA_PORT` no `.env` antes de tentar de novo. Confira a porta
> publicada de fato (`docker compose ps`, coluna `PORTS`) antes de preencher — e some a
> **mesma** origem em **CORS allowed origins**. Redirect com a porta errada dá erro do Logto
> na volta do login ("redirect URI mismatch"), fácil de confundir com problema de App ID.

![Tela de configurações do aplicativo V3RSec PWA no console do Logto, mostrando os campos Redirect URIs, Post sign-out redirect URIs e CORS allowed origins preenchidos com a porta 8081](/assets/bootstrap-logto-07.png)

### 8. Registrar o recurso de API do backend

Crie um **API Resource** no Logto com exatamente o mesmo identificador configurado em
`LOGTO_API_RESOURCE` (padrão `urn:v3rsec:api`). É esse "audience" que o backend Deno valida
no token OIDC (via JWKS) para autorizar as chamadas do PWA.

![Tela de detalhes do recurso de API no console do Logto, mostrando o identificador urn:v3rsec:api preenchido](/assets/bootstrap-logto-08.png)

### 9. Reconstruir o PWA (App ID é lido em tempo de build)

O `VITE_LOGTO_APP_ID` é lido **em tempo de build** do PWA, não em runtime. Se você o
preencheu ou alterou agora, reconstrua o container — senão o pacote continua rodando com o
valor antigo (ou vazio) e o login falha silenciosamente, sem log nenhum que aponte para
isso:

```bash
docker compose up -d --build v3rsec-pwa
```

## Exemplo — identificadores de uma instalação real

Só para você reconhecer o formato; **os valores abaixo são desta instalação específica e
não devem ser copiados** — cada instalação gera os seus:

| Item | Valor de exemplo |
|---|---|
| Organização (V3RTECH) | `or2ivomanag5` |
| Aplicativo SPA "V3RSec PWA" | `5hhp30h3qztxjhvtsrph6` |
| API Resource | `urn:v3rsec:api` |

## Validar

Abra o PWA na porta em que ele foi publicado de fato (confira com `docker compose ps`),
faça login com o usuário criado no passo 4 e confirme que você cai na tela inicial do
V3RSec (**Saúde dos alvos**).

## Quando dá errado

| Sintoma | Causa mais provável | O que fazer |
|---|---|---|
| Botão de criar (organização, papel, app...) não faz nada, sem erro na tela | Você está em `http://localhost:3302` em vez de `http://admin.localhost:3302` | Troque para `admin.localhost` e recarregue |
| Busca por usuário na tela de Members retorna "No data" | Você está tentando adicionar a conta de administrador do console (tenant `admin`), não um usuário do V3RSec (tenant `default`) | Crie o usuário em User management (passo 4) e busque por ele |
| Login passa no Logto, mas o V3RSec responde **401** | Falta o `INSERT` em `idp_organizations` ligando a organização ao tenant (passo 6) | Rode o `INSERT` do passo 6 no Postgres do V3RSec |
| Login funciona, usuário cai no V3RSec, mas não consegue fazer nada | O usuário foi adicionado como membro da organização, mas não teve papéis atribuídos (passo 5.2), ou o nome do papel não bate com `operador_seguranca` / `admin_plataforma` / `visualizador` | Confira "Edit organization roles" do membro e o nome exato do papel |
| Erro de "redirect URI mismatch" na volta do login | O redirect cadastrado no app SPA usa uma porta diferente da porta real em que o PWA subiu | Confira a porta publicada (`docker compose ps`) e ajuste redirect + CORS allowed origins (passo 7) |
| Login funciona mas alguém que deveria logar recebe erro genérico | `VITE_LOGTO_APP_ID` desatualizado no container do PWA | Confirme o valor no `.env` e reconstrua o PWA (passo 9) |

## Glossário

- **Tenant (Logto)** — um espaço isolado dentro do Logto. Este bootstrap usa dois: o tenant
  `admin` (administra o Logto) e o tenant `default` (onde vivem os usuários das
  aplicações). Não confundir com **tenant do V3RSec**, que é o conceito de cliente/condomínio
  isolado no produto.
- **Organização (Logto)** — dentro do tenant `default`, agrupa usuários e papéis. No V3RSec,
  cada organização do Logto corresponde a um tenant do V3RSec — o vínculo é o `INSERT` do
  passo 6.
- **App ID** — identificador público do aplicativo SPA cadastrado no Logto. Não é segredo,
  mas é lido em tempo de build do PWA, então trocar o valor exige reconstruir o container.
- **API Resource / audience** — identificador que o backend usa para validar que um token
  foi emitido para ele, e não para outro serviço.
- **M2M (machine-to-machine)** — tipo de credencial do Logto para automação server-to-server
  (por exemplo, criar organizações via API em vez de manualmente). Ainda não usada neste
  bootstrap — ver seção seguinte.

## O que ainda é manual / futuro

- **Onboarding programático de organização** (criar org via Management API do Logto,
  vinculando org ↔ tenant automaticamente, sem o `INSERT` manual do passo 6) ainda é um
  stub — hoje o vínculo é manual. Exige uma credencial **M2M de admin** do Logto
  (`LOGTO_MANAGEMENT_API_M2M_*`), fora do escopo desta fatia.
- **Seleção de tenant** para um usuário que pertence a **várias organizações** ainda não
  existe — o V3RSec usa a primeira organização do token.
