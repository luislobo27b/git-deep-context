# Modelo de commit

Fonte da verdade para o formato: **Conventional Commits 1.0.0** e **commitlint v0.11.0** — documentos originais em [sources/conventionalcommits-v1.md](sources/conventionalcommits-v1.md) e [sources/commitlint-v0.11.0.md](sources/commitlint-v0.11.0.md). As listas de types e escopos abaixo são a extensão definida para este projeto — o commitlint padrão exige o formato e restringe os types a uma lista curta (`type-enum`); estas tabelas ampliam esse conjunto mantendo a mesma estrutura.

## Formato

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

Regras do formato (commitlint v0.11.0):

- **Header** (primeira linha): mínimo de 10 e máximo de **72 caracteres**. Type obrigatório, seguido de `: ` (dois-pontos e espaço). Escopo opcional, entre parênteses logo após o type.
- **Descrição:** resumo curto e objetivo da alteração, sem ponto final.
- **Corpo:** opcional; começa após uma linha em branco; cada linha de no máximo **100 caracteres**; nesta skill, o corpo usa bullets iniciados por `- `.
- **Rodapé(s):** opcionais, após linha em branco (ex.: `Refs #133`, `BREAKING CHANGE: ...`), conforme o formato git trailer.
- Type sempre em minúsculas (`feat`, não `Feat`).

Indicadores de breaking change (Conventional Commits): `!` antes dos dois-pontos (`feat(api)!: ...`) ou o rodapé `BREAKING CHANGE: <descrição>`. Use apenas quando a mudança realmente quebrar compatibilidade.

## Types (prefixos)

Os types indicam a natureza da alteração. Escolha o que melhor representa o objetivo principal do commit.

### Types prioritários

A primeira tabela contém os types prioritários: use um deles sempre que fizer sentido. A segunda contém categorias específicas, utilizadas somente quando forem o motivo exclusivo do commit ou quando não houver um type prioritário mais adequado.

| Type        | Descrição                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------ |
| `init`      | Inicialização do projeto ou primeiro commit                                                |
| `feat`      | Adição de funcionalidades                                                                  |
| `fix`       | Correção de bugs                                                                           |
| `hotfix`    | Correção crítica e urgente                                                                 |
| `docs`      | Documentação técnica                                                                       |
| `style`     | Formatação do código sem alterar o comportamento                                           |
| `refactor`  | Refatoração e mudanças estruturais internas                                                |
| `perf`      | Melhorias de performance                                                                   |
| `test`      | Criação, atualização e correção de testes                                                  |
| `build`     | Compilação e empacotamento                                                                 |
| `ci`        | Configuração e correções de integração contínua                                            |
| `chore`     | Manutenção geral sem categoria mais específica                                             |
| `revert`    | Reversão de alterações anteriores                                                          |
| `ux/ui`     | Interface, experiência do usuário, responsividade, animações, usabilidade e acessibilidade |
| `security`  | Correções e melhorias de segurança e privacidade                                           |
| `lint`      | Correção de problemas de lint e avisos estáticos                                           |
| `config`    | Configurações do projeto                                                                   |
| `deps`      | Adição, remoção, atualização, downgrade ou fixação de dependências                         |
| `analytics` | Instrumentação, eventos e métricas                                                         |
| `text`      | Textos e literais da aplicação                                                             |
| `assets`    | Recursos estáticos, imagens e ícones                                                       |
| `logs`      | Adição, atualização ou remoção de logs                                                     |
| `mocks`     | Criação e atualização de mocks                                                             |
| `types`     | Tipos e definições                                                                         |
| `schema`    | Criação, atualização e validação de schemas e estruturas de dados                          |
| `api`       | Alterações em APIs, integrações e contratos                                                |
| `data`      | Banco de dados, persistência e estruturas de dados                                         |
| `infra`     | Infraestrutura e ambiente de execução                                                      |
| `auth`      | Autenticação, autorização e permissões                                                     |

### Types específicos

Sugira estes apenas quando a alteração se encaixar exclusivamente em uma dessas categorias. Se o commit também se enquadrar em um type prioritário da tabela acima, sugira o type prioritário como primeira opção e o específico como alternativa.

| Type          | Descrição                                     |
| ------------- | --------------------------------------------- |
| `business`    | Regras e lógica de negócio                    |
| `deploy`      | Implantação e publicação                      |
| `release`     | Preparação de releases e versionamento        |
| `merge`       | Integração de branches                        |
| `healthcheck` | Verificações de integridade e disponibilidade |
| `snapshot`    | Criação e atualização de snapshots de testes  |
| `license`     | Licenças e avisos legais                      |

## Escopos

Os escopos indicam a área, o módulo ou o contexto afetado pela alteração. São opcionais e devem ser utilizados quando ajudarem a tornar a mensagem mais específica. Os exemplos listados são sugestões, não uma lista fechada: você pode usar outros escopos quando necessário, mantendo nomes curtos, consistentes e em inglês.

| Type          | Escopos sugeridos                                                   |
| ------------- | ------------------------------------------------------------------- |
| `init`        | `project`, `app`, `scaffold`                                        |
| `feat`        | `ui`, `api`, `auth`, `data`, `ai`, `config`                         |
| `fix`         | `ui`, `api`, `auth`, `data`, `ai`, `config`                         |
| `hotfix`      | `ui`, `api`, `auth`, `data`, `infra`                                |
| `docs`        | `readme`, `guides`, `api`, `ai`, `instructions`                     |
| `style`       | `ui`, `formatting`                                                  |
| `refactor`    | `ui`, `api`, `auth`, `data`, `ai`, `architecture`                   |
| `perf`        | `ui`, `api`, `data`, `build`, `runtime`                             |
| `test`        | `unit`, `integration`, `e2e`, `api`, `ui`                           |
| `build`       | `bundling`, `compilation`, `packaging`, `dependencies`              |
| `ci`          | `pipeline`, `checks`, `tests`, `automation`                         |
| `chore`       | `maintenance`, `tooling`, `repository`                              |
| `revert`      | Escopo da alteração revertida, quando aplicável                     |
| `ux/ui`       | `components`, `layout`, `responsive`, `animations`, `accessibility` |
| `security`    | `auth`, `permissions`, `validation`, `dependencies`, `api`          |
| `lint`        | `formatting`, `rules`, `code-quality`                               |
| `config`      | `env`, `gitignore`, `tooling`, `ai`                                 |
| `deps`        | `ui`, `data`, `api`, `build`                                        |
| `analytics`   | `tracking`, `events`, `metrics`, `telemetry`, `seo`                 |
| `text`        | `copy`, `content`, `labels`, `messages`, `license`                  |
| `assets`      | `images`, `icons`, `fonts`, `illustrations`                         |
| `logs`        | `logging`, `debug`, `monitoring`                                    |
| `mocks`       | `api`, `fixtures`, `factories`, `data`                              |
| `types`       | `interfaces`, `definitions`                                         |
| `schema`      | `validation`, `contracts`, `serialization`, `definitions`           |
| `api`         | `rest`, `graphql`, `webhooks`, `integrations`                       |
| `data`        | `database`, `migrations`, `queries`, `seeds`                        |
| `infra`       | `containers`, `hosting`, `network`, `server`                        |
| `auth`        | `authentication`, `authorization`, `permissions`, `roles`           |
| `business`    | `billing`, `checkout`, `pricing`, `rules`                           |
| `deploy`      | `staging`, `production`, `hosting`, `release`                       |
| `release`     | `versioning`, `changelog`, `tagging`                                |
| `merge`       | `main`, `develop`, `release`                                        |
| `healthcheck` | `api`, `database`, `readiness`, `liveness`                          |
| `snapshot`    | `ui`, `visual`, `regression`                                        |
| `license`     | `project`, `dependencies`, `third-party`                            |

Os escopos são opcionais e devem ser utilizados apenas se acrescentarem valor ao commit; caso contrário, omita o escopo.

### Escopos de IA

Os itens abaixo são **exclusivamente escopos**, nunca types de commit. Podem ser usados com qualquer type desde que faça sentido — o mais comum é `docs`, mas pode ser `feat`, `refactor`, etc.

- `agents` — Agentes, skills e instruções (AGENTS.md, SKILL.md etc.).
- `artifacts` — Arquivos e resultados gerados.
- `commands` — Comandos invocáveis.
- `loop` — Ciclos iterativos de execução de agentes.
- `models` — Modelos de IA.
- `permissions` — Permissões e controles de acesso.
- `prompts` — Prompts e templates de IA.
- `references` — Documentação e materiais de consulta.
- `rules` — Regras e convenções específicas.
- `scripts` — Scripts e automações executáveis.
- `skills` — Skills do agente e seus arquivos (pasta skills/ e SKILL.md).
- `tasks` — Tarefas delegáveis ou unidades de trabalho atribuídas a agentes, incluindo definições, instruções e acompanhamento de execução.
- `tools` — Ferramentas, plugins e integrações MCP.
- `workflows` — Pipelines e fluxos de execução.
- `hooks` — Código executado automaticamente em eventos ou pontos específicos do ciclo de vida de uma ferramenta.

## Formato com corpo

```text
<type>(<scope>): <descrição>
- <detalhe da alteração>
- <detalhe adicional>
- <contexto ou justificativa>
```

- **Type:** indica a natureza da alteração, como `feat`, `fix` ou `refactor`.
- **Escopo:** identifica a área afetada e é opcional.
- **Descrição:** resume a principal alteração de forma curta e objetiva.
- **Corpo:** detalha as mudanças em bullet points iniciados por `- `. É opcional, mas recomendado para commits mais complexos ou que precisem de contexto adicional.

### Exemplos completos

**1. Nova funcionalidade**

```text
feat(ui): add responsive navigation
- Add a collapsible menu for mobile devices
- Implement navigation between application routes
- Improve keyboard navigation and accessibility
- Preserve the desktop layout and existing interactions
```

**2. Correção de bug**

```text
fix(auth): handle expired access tokens
- Detect expired access tokens before making protected requests
- Attempt to refresh the token when a refresh token is available
- Redirect to the login page when the session cannot be restored
- Prevent repeated requests after authentication failure
```

**3. Refatoração com melhoria de performance**

```text
refactor(dashboard): optimize data rendering
- Extract reusable components for dashboard widgets
- Memoize expensive data transformations
- Prevent unnecessary re-renders when unrelated state changes
- Preserve existing functionality and visual behavior
```

### Diretrizes

- Escreva o cabeçalho de forma concisa, resumindo o objetivo principal do commit.
- Utilize o corpo para explicar **o que foi alterado**, **como foi alterado** ou **por que a mudança foi necessária**.
- Mantenha cada bullet point focado em uma informação específica.
- Não repita no corpo a descrição do cabeçalho sem acrescentar detalhes.
- Prefira verbos no imperativo em inglês, mantendo consistência entre os commits.
- Não inclua bullet points vazios nem informações irrelevantes.
- Para alterações simples, um cabeçalho sem corpo é suficiente.
- Header sempre dentro do limite de 72 caracteres do commitlint — encurte escopo ou descrição em vez de quebrar a linha.

Ver também o modelo de resumo em [summary.md](summary.md).
