# Manual do usuário — V3RSec

Site do manual do usuário do **V3RSec** (monitoramento de segurança e detecção de vazamento de
dados da V3RTECH). Publicado via **GitHub Pages** em <https://docs.v3rsec.v3rtech.com.br/>.

- **Stack:** Jekyll + tema [just-the-docs](https://just-the-docs.com) (remote theme).
- **Design-system:** próprio do V3RSec (não o do V3REvent) — cores em `color_scheme: v3rsec`
  (`_sass/color_schemes/v3rsec.scss`), tipografia **Exo 2** (títulos) e **Open Sans** (corpo),
  auto-hospedadas em `assets/fonts/`. A logo é a **definitiva**, servida de
  `https://assets.v3rsec.v3rtech.com.br/` (não uma cópia local nem placeholder).
- **Modelo de repositório:** este diretório **é** o clone do repo `V3RTECH-DF/V3RSec-Manual`,
  um dos três repositórios independentes do container V3RSec (ver `CLAUDE.md` na raiz do
  container). Editar aqui e empurrar já publica (o workflow de Pages rebuilda). O script de
  publicação (`publish-manual.sh`) mora em `Projeto/bin/`, fora deste repositório.

## Visualizar localmente

```bash
bundle install
bundle exec jekyll serve
# abre http://127.0.0.1:4000/
```

## Publicar

Pelo script do projeto, a partir de `Projeto/bin/` (não deste repositório):

```bash
Projeto/bin/publish-manual.sh                 # commita+empurra o pendente neste diretório
Projeto/bin/publish-manual.sh -m "mensagem"   # mensagem de commit personalizada
```

O push na branch `main` dispara o workflow `.github/workflows/pages.yml`, que builda o Jekyll e
publica no GitHub Pages.

## Estrutura

- `index.md` — página inicial.
- `operador/` — guia do Operador de Segurança.
- `admin/` — guia do Admin de Plataforma (subir a stack, bootstrap do Logto).
- `referencia/` — arquitetura, estado atual/limitações, novidades, FAQ, privacidade.
- `assets/` — favicon, fontes auto-hospedadas, capturas já feitas e `README-screenshots.md`
  (roteiro de capturas pendentes). A logo não fica aqui: é servida de
  `https://assets.v3rsec.v3rtech.com.br/`.
- `_sass/`, `_includes/`, `_config.yml` — tema e configuração (design-system próprio do V3RSec).
