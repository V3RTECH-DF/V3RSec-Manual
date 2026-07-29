# Manual do usuário — V3RSec

Site do manual do usuário do **V3RSec** (monitoramento de segurança e detecção de vazamento de
dados da V3RTECH). Publicado via **GitHub Pages** em <https://docs.v3rsec.v3rtech.com.br/>.

- **Stack:** Jekyll + tema [just-the-docs](https://just-the-docs.com) (remote theme).
- **Design-system:** oficial da V3RTECH (mesmo do V3REvent — cores, tipografia Exo 2/Inter).
  A logo é **placeholder** (`assets/logo.svg`) até a definitiva ser criada.
- **Modelo de repositório:** este diretório **é** o clone do repo `V3RTECH-DF/v3rsec-manual`.
  Editar aqui e empurrar já publica (o workflow de Pages rebuilda). No projeto V3RSec, `manual/`
  é gitignored (não faz parte do repo de código) — ver `bin/publish-manual.sh`.

## Visualizar localmente

```bash
bundle install
bundle exec jekyll serve
# abre http://127.0.0.1:4000/
```

## Publicar

Pelo script do projeto (na raiz do V3RSec):

```bash
bin/publish-manual.sh                 # commita+empurra o pendente em manual/
bin/publish-manual.sh -m "mensagem"   # mensagem de commit personalizada
```

O push na branch `main` dispara o workflow `.github/workflows/pages.yml`, que builda o Jekyll e
publica no GitHub Pages.

## Estrutura

- `index.md` — página inicial.
- `operador/` — guia do Operador de Segurança.
- `admin/` — guia do Admin de Plataforma (subir a stack, bootstrap do Logto).
- `referencia/` — arquitetura, estado atual/limitações, novidades, FAQ, privacidade.
- `assets/` — logo/favicon (placeholder) e `README-screenshots.md` (roteiro de capturas pendentes).
- `_sass/`, `_includes/`, `_config.yml` — tema e configuração (design-system V3RTECH).
