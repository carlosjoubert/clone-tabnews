# AGENTS.md

Este repositório é uma aplicação Next.js para clonar a funcionalidade principal do TabNews, com foco em APIs REST e banco de dados PostgreSQL.

## Visão geral do projeto

- App principal em Next.js com roteamento em `pages/`.
- Rotas de API em `pages/api/`.
- Banco de dados centralizado em `infra/database.js`.
- Infra local do PostgreSQL configurada em `infra/compose.yaml`.
- Migrações em `infra/migrations/` usando `node-pg-migrate`.
- Testes de integração em `tests/integration/`.

## Stack e convenções

- Node.js: compatível com a versão definida em `.nvmrc` (`lts/hydrogen`).
- Next.js 13 (pages router, não app router).
- PostgreSQL 16.
- Jest para testes.
- Prettier para formatação.
- Estilo de código: indentação com 2 espaços, ponto e vírgula, preferencialmente trailing commas em objetos/arrays multilinha.
- Use imports com alias quando o projeto já o usar, por exemplo `import database from "infra/database.js"`.

## Comandos úteis

- Instalar dependências: `npm install`
- Subir banco local: `npm run services:up`
- Parar banco local: `npm run services:stop`
- Desligar banco local: `npm run services:down`
- Iniciar app em desenvolvimento: `npm run dev`
- Executar migrações: `npm run migration:up`
- Verificar formatação: `npm run lint:check`
- Executar testes: `npm test`

## Regras de desenvolvimento

- Mantenha a arquitetura em camadas simples: páginas/rotas e acesso ao banco em `infra/`.
- Para endpoints da API, siga o padrão de `request.method` e retornar `response.status(...).json(...)` ou `response.status(405).end()` para métodos não suportados.
- Ao acessar o banco, prefira usar o helper em `infra/database.js` em vez de criar clientes PostgreSQL diretamente em outros arquivos.
- Respeite o contrato existente das respostas JSON; testes de integração costumam verificar campos específicos como `updated_at`, `dependecies.database.version`, etc.
- Não edite artefatos gerados em `.next`, `node_modules` ou arquivos de build.
- Use nomes consistentes com o projeto; `api/v1/status` e `api/v1/migrations` já definem a convenção de rotas.

## Testes

- Testes ficam em `tests/integration/api/...`.
- Os testes de integração acessam a aplicação em `http://localhost:3000`.
- Ao adicionar nova funcionalidade, prefira cobrir com testes relativamente pequenos, focados em comportamento e contrato da API.
- Antes de concluir mudanças, execute o menor conjunto de testes que cubra a alteração.

## Observações específicas do projeto

- O Postgres local é configurado via `.env.development` e o serviço Docker em `infra/compose.yaml`.
- O arquivo `pages/api/v1/migrations/index.js` usa `node-pg-migrate` com `dryRun: true` em desenvolvimento; mantenha esse comportamento ao alterar a rota.
- O helper de conexão `infra/database.js` usa `ssl` condicionalmente com base em `POSTGRES_CA` e em `NODE_ENV`.
