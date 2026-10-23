---
name: git-deep-context
description: "Analisa o histórico, as mudanças e o contexto do repositório para sugerir commits atômicos seguindo Conventional Commits, com motivações claras. Use quando o usuário quiser transformar alterações pendentes (staged, unstaged ou não rastreadas) em sugestão(ões) de commit(s) com mensagem e resumo, entender o padrão de commits do projeto ou montar mensagens aderentes a ele. Não reescreve histórico: apenas lê e cria commits novos."
version: 1.0.0
user-invocable: true
argument-hint: "[contexto adicional opcional]"
allowed-tools:
  - Bash(git status *)
  - Bash(git add *)
  - Bash(git commit *)
  - Bash(git log *)
  - Bash(git show *)
  - Bash(git diff *)
  - Bash(git grep *)
  - Bash(git ls-files *)
  - Bash(git ls-tree *)
---

Esta skill analisa profundamente o repositório atual e transforma essa análise em sugestão(ões) de commit(s) atômico(s): a mensagem no padrão Conventional Commits, aderente ao padrão do projeto, e um resumo das motivações em linguagem simples.

Princípios:

- O histórico pode avançar, mas nunca ser reescrito.
- Um commit = uma intenção atômica; misturas de tipos diferentes viram commits separados.
- O padrão consistente do projeto prevalece; sem padrão consistente, vale rigidamente o modelo documentado na referência.
- Quando há arquivos staged, a sugestão cobre apenas esses arquivos.

## Objetivo

Analisar profundamente o repositório atual — histórico de commits, alterações staged e unstaged, arquivos novos e o contexto temporal do projeto (como o código evoluiu, o que mudou recentemente, quais decisões já foram registradas) — e produzir sugestão(ões) de commit(s) atômico(s) seguindo Conventional Commits, cada um com sua mensagem no padrão do projeto e um resumo das motivações em linguagem simples.

A skill não se limita a nomear o diff: ela entende o que mudou e por que mudou, agrupa as mudanças por intenção única (atomicidade), adere ao padrão de mensagens já estabelecido no histórico e respeita a política de permissões git abaixo — somente leitura e criação de commits novos. O mesmo fluxo serve para revisar o que está prestes a virar commit ou o que uma branch introduz antes de um pull request — sempre por leitura local, sem push.

## Política de permissões git

### Objetivo da política

Preservar a integridade do histórico do repositório, impedindo a alteração, remoção, substituição ou reescrita de commits existentes. A política estabelece que o histórico deve evoluir exclusivamente de forma **aditiva e progressiva**: novos commits podem ser criados, mas registros existentes devem permanecer intactos.

Por que isso é inegociável nesta skill: o trabalho dela se baseia justamente na leitura confiável do passado (`git log`, `git show`, `git diff`). Se qualquer operação pudesse reescrever, descartar ou mover registros, a análise deixaria de ser confiável e alterações do usuário poderiam ser perdidas sem aviso. Esta política se aplica a toda execução da skill, sem exceção — inclusive quando um comando negado parecer o caminho mais rápido.

### Comandos permitidos

- `git status` — Consultar o estado do repositório.
- `git add` — Preparar alterações para um novo commit.
- `git commit` — Criar um novo commit, sem alterar os anteriores.
- `git log` — Consultar o histórico de commits.
- `git show` — Inspecionar commits e objetos Git.
- `git diff` — Comparar alterações e versões.
- `git grep` — Pesquisar conteúdo nos arquivos versionados.
- `git ls-files` — Listar arquivos rastreados.
- `git ls-tree` — Inspecionar a estrutura de arquivos registrada.

### Comandos negados

- `git restore`
- `git rm`
- `git mv`
- `git branch`
- `git switch`
- `git checkout`
- `git merge`
- `git rebase`
- `git remote`
- `git fetch`
- `git pull`
- `git push`
- `git push --force`
- `git push --force-with-lease`
- `git reset`
- `git reset --soft`
- `git reset --mixed`
- `git reset --hard`
- `git revert`
- `git stash`
- `git cherry-pick`
- `git clean`
- `git commit --amend`
- `git filter-branch`

### Regra fundamental

Somente operações de leitura e a criação de novos commits são autorizadas. É proibida qualquer operação que altere ou elimine registros existentes, descarte alterações ou reescreva o histórico.

**Princípio: o histórico pode avançar, mas nunca ser reescrito.**

## Comandos obrigatórios de contexto

O objetivo da investigação é **revisar as alterações antes de um commit ou pull request** (a revisão pré-PR é local: apenas leitura, sem push). Execute estes comandos de leitura por ordem de prioridade, para que a sugestão nasça do contexto completo do repositório, não só do diff visível — todos são permitidos pela política acima.

| Prioridade | Comando                                        | Objetivo                                                                                                            | Quando                                    |
| ---------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| 1          | `git status -sb`                               | Entender o estado do repositório: staged, unstaged, não rastreados e relação com a upstream                         | Sempre                                    |
| 2          | `git log -20 --pretty=format:"%h %s%n%b%n---"` | Ler as últimas 20 mensagens e analisar o padrão de commits do projeto                                               | Sempre                                    |
| 3          | `git diff`                                     | Revisar alterações locais ainda fora do stage                                                                       | Sempre                                    |
| 4          | `git diff --staged`                            | Revisar o conteúdo preparado para commit                                                                            | Sempre                                    |
| 5          | `git diff HEAD`                                | Revisar alterações rastreadas em relação ao último commit                                                           | Sempre                                    |
| 6          | `git diff --stat`                              | Avaliar rapidamente o tamanho das mudanças (`git diff --staged --stat` para o conjunto staged)                      | Sempre                                    |
| 7          | `git diff --check`                             | Verificar problemas de whitespace                                                                                   | Sempre                                    |
| 8          | `git diff main...HEAD`                         | Revisar o que a branch atual introduz em relação à base comum com `main`                                            | Quando houver branch de trabalho (pré-PR) |
| 9          | `git log --oneline --graph --all`              | Entender a estrutura do histórico                                                                                   | Sempre                                    |
| 10         | `git log --oneline main..HEAD`                 | Listar os commits que a branch adiciona em relação à `main`                                                         | Quando houver branch de trabalho (pré-PR) |
| 11         | `git show HEAD`                                | Inspecionar o último commit                                                                                         | Sempre                                    |
| 12         | `git ls-files --others --exclude-standard`     | Listar arquivos novos ainda não rastreados                                                                          | Sempre                                    |
| 13         | `git grep <termo>`                             | Localizar onde um conceito já existe no código versionado                                                           | Quando necessário                         |
| 14         | `git log --oneline -10 -- <arquivo>`           | Entender a origem e a evolução temporal de um arquivo (o que substitui o `blame`: foco no conteúdo, não na autoria) | Quando ajudar a decidir type/scope        |

Padrão do projeto: a partir das 20 mensagens do `git log` (prioridade 2), identifique o padrão — types usados, uso de escopo, caixa, idioma e presença de corpo. Se o padrão for consistente, siga-o (sempre dentro do formato Conventional Commits). Se não houver padrão consistente, provavelmente o projeto está começando a usar este novo sistema: nesse caso, siga rigidamente o modelo de [references/commit-convention.md](references/commit-convention.md).

## Workflow

1. **Estado inicial:** rode `git status -sb` e `git diff --staged --name-only`. Se houver arquivos staged, aplique a [Regra de staged](#regra-de-staged-precedência-máxima) — o escopo da sugestão passa a ser apenas esses arquivos, e as demais alterações são só informadas como pendentes fora do staged.
2. **Contexto:** execute os comandos da tabela Comandos obrigatórios de contexto sobre o escopo definido no passo 1 e leia o conteúdo das mudanças (incluindo arquivos não rastreados).
3. **Padrão:** analise as últimas 20 mensagens e defina qual padrão de commit o projeto segue (consistente → siga-o; inconsistente → siga rigidamente a referência).
4. **Atomicidade:** agrupe as mudanças por intenção única — cada grupo vira um commit. Separe o que misturar tipos diferentes (uma feature que também corrige um bug e formata código vira três commits). Com arquivos staged, o conjunto é um único commit: a política não permite desfazer stage.
5. **Mensagem:** carregue [references/commit-convention.md](references/commit-convention.md) e monte a mensagem de cada commit (header de no máximo 72 caracteres; corpo em bullets `- ` quando a mudança pedir contexto).
6. **Resumo:** carregue [references/summary.md](references/summary.md) e produza o resumo das motivações.
7. **Entrega:** apresente o(s) commit(s) e o resumo. Rode `git add`/`git commit` somente após aprovação explícita do usuário. Nunca execute um comando negado, mesmo que pareça necessário para "desfazer" algo — informe o impasse em vez de contornar a política.

## Regra de staged (precedência máxima)

Sempre que `git diff --staged --name-only` retornar ao menos um arquivo, esta regra se aplica **antes de qualquer outra análise**:

1. A sugestão cobre **apenas os arquivos já staged**. Não proponha commits para alterações unstaged ou arquivos não rastreados; apenas informe, ao final, que existem outras alterações fora do staged e que não fazem parte desta sugestão.
2. O conjunto staged vira **um único commit sugerido** — `git restore` e `git reset` são negados pela política, então nada é tirado do stage.
3. A apresentação **deve começar** com a frase exata, nesta grafia:

   > Considerando os arquivos já em staged o commit que eu sugiro é ...

   e logo em seguida vêm a mensagem de commit sugerida e o resumo.

4. Se não houver nada staged, esta regra não se aplica: analise todas as alterações (staged, unstaged e não rastreadas) e sugira a separação em commits atômicos.

## Referências

| Reference                                                          | Conteúdo                                                                                                         | Carregar quando                               |
| ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- | --------------------------------------------- |
| [references/commit-convention.md](references/commit-convention.md) | Types, escopos, formato, diretrizes e exemplos de mensagem (fonte da verdade: Conventional Commits + commitlint) | Sempre, ao montar qualquer mensagem de commit |
| [references/summary.md](references/summary.md)                     | Modelo do resumo das motivações, regras e exemplos                                                               | Sempre, ao produzir o resumo                  |

Os documentos fonte do padrão estão em `references/sources/` ([commitlint-v0.11.0.md](references/sources/commitlint-v0.11.0.md), [conventionalcommits-v1.md](references/sources/conventionalcommits-v1.md)) — consulte-os em caso de conflito ou dúvida sobre a especificação; a interpretação operacional está em `references/commit-convention.md`.
