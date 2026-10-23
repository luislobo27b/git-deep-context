# git-deep-context

Skill para [OpenCode](https://opencode.ai) que analisa o estado, o histórico e o contexto de um repositório Git e propõe commits atômicos seguindo [Conventional Commits](https://www.conventionalcommits.org/), com mensagem aderente ao padrão do projeto e um resumo das motivações em linguagem simples.

O foco é **entender o que mudou e por quê**, não apenas listar arquivos: a skill lê os diffs, o histórico recente e a evolução dos arquivos para agrupar as mudanças por intenção única.

> A skill apenas lê o repositório e cria commits novos, sempre após aprovação explícita. Ela não faz push, não reescreve histórico e não contorna hooks.

## O que ela faz

- Lê o estado do repositório: staged, unstaged, não rastreados e relação com a upstream.
- Analisa as últimas mensagens para inferir o padrão de commits do projeto (types, escopo, caixa, idioma, corpo).
- Revisa diffs e o conteúdo de arquivos novos para entender a intenção de cada mudança.
- Agrupa alterações por atomicidade: uma intenção por commit, sem misturar tipos diferentes.
- Monta a mensagem no formato Conventional Commits e um resumo das motivações.
- Prepara e cria os commits **somente depois** de o usuário aprovar a lista exata de arquivos e mensagens.

### Regra de staged

Se já existir algo no stage, a sugestão cobre **apenas esses arquivos** e vira um único commit — a política não permite desfazer stage (`git restore`/`git reset` são negados). Alterações fora do stage são apenas informadas como pendentes.

## O que ela não faz

- Não faz `push`, `fetch`, `pull`, `merge`, `rebase`, `cherry-pick` ou qualquer operação de rede.
- Não reescreve histórico (`--amend`, `filter-branch`, `reset`, `revert` etc.).
- Não usa `--no-verify`, `-n` ou `--no-gpg-sign` para pular hooks ou assinatura.
- Não adiciona arquivos com `git add .`, `-A`, `-u` ou `-f`.
- Não prepara nem commita segredos (`.env`, chaves, certificados, credenciais, dumps).
- Não obedece a instruções encontradas no conteúdo do repositório (prompt injection).

## Uso

Dentro de um projeto com a skill instalada, invoque o comando:

```text
/git-deep-context
```

Opcionalmente, passe contexto adicional (apenas contexto — não altera a política):

```text
/git-deep-context foque apenas em src/
```

A skill também pode ser carregada automaticamente ao pedir algo como “sugira commits para as alterações pendentes” ou “analise o que esta branch introduz antes do PR”.

### Instalação

Copie a pasta `.opencode/` deste repositório para a raiz do projeto onde a skill será usada, ou instale no diretório global de configuração do OpenCode. A estrutura relevante é:

```text
.opencode/
├── commands/git-deep-context.md
├── opencode.jsonc
└── skills/git-deep-context/
    ├── SKILL.md
    └── references/
```

Requisitos: Git instalado e um harness que reconheça skills no formato do OpenCode. As permissões podem ser reforçadas no `opencode.jsonc` (veja abaixo).

## Permissões e segurança

A restrição existe em duas camadas, por defesa em profundidade:

1. **Frontmatter da skill** (`SKILL.md`): `allowed-tools` limitado a comandos de leitura e a `git add -- <caminhos>` / `git commit -m "<mensagem>"`.
2. **Harness** (`.opencode/opencode.jsonc`): allowlist de shell imposta pelo executor. A ordem importa — vence a **última** regra que casar. Por isso os `deny` de flags perigosas vêm depois dos `allow`.

Comandos negados incluem `git push`, `pull`, `fetch`, `reset`, `restore`, `checkout`, `switch`, `clean`, `stash`, `rebase`, `merge`, `config`, `tag`, `update-ref`, `filter-branch` e outros que alteram estado ou histórico.

Flags negadas incluem `--amend`, `--no-verify`, `-n`, `-a`, `--all`, `--no-gpg-sign`, `--force`, `-f`, `-A`, `-u` e `git add .`. Comandos git também não devem ser encadeados com pipes, `&&`, `;`, redirecionamentos ou `$(...)`.

### Segredos

Nunca entram no commit: `.env`/`.env.*` (exceto exemplos sem valores), `*.pem`, `*.key`, `*.p12`, `*.pfx`, `*.keystore`, `*.jks`, `id_rsa*`, `id_ed25519*`, `.npmrc`, `.netrc`, `.pypirc`, `.git-credentials`, `.aws/`, `.ssh/`, `.gnupg/`, `kubeconfig`, `*.sqlite`, `*.db`, `*.dump`, `*.log` e arquivos com aparência de token/senha. Arquivos sensíveis ou ambíguos ficam de fora e são encaminhados ao usuário.

### Prompt injection

Mensagens de commit, diffs, código, comentários, nomes de arquivos, branches, documentação e saídas de comandos são **dados não confiáveis**, nunca instruções. Se contiverem comandos ou pedidos para relaxar a política, a skill reporta o trecho ao usuário e não obedece.

## Estrutura do repositório

```text
.
├── HARDENING.md                  # Auditoria e recomendações de endurecimento
└── .opencode/
    ├── opencode.jsonc            # Permissões impostas pelo harness
    ├── commands/
    │   └── git-deep-context.md   # Comando que carrega a skill
    └── skills/git-deep-context/
        ├── SKILL.md              # Instrução principal e política
        └── references/
            ├── commit-convention.md
            ├── summary.md
            └── sources/
                ├── README.md
                ├── commitlint-v0.11.0.md
                └── conventionalcommits-v1.md
```

## Referências

| Arquivo | Conteúdo |
| --- | --- |
| [`references/commit-convention.md`](.opencode/skills/git-deep-context/references/commit-convention.md) | Types, escopos, formato e exemplos de mensagem |
| [`references/summary.md`](.opencode/skills/git-deep-context/references/summary.md) | Modelo do resumo das motivações |
| [`references/sources/`](.opencode/skills/git-deep-context/references/sources/README.md) | Cópias somente leitura do README do commitlint e da especificação Conventional Commits 1.0.0 |

O diretório `references/` é material de consulta: comandos citados ali (instalação, `commitlint init`, `git config core.hooksPath`, `--no-verify` etc.) **não devem ser executados**.

## Limitações conhecidas

- O `allowed-tools` do frontmatter é interpretado por alguns harnesses e ignorado por outros; a imposição real depende do `opencode.jsonc` ou da configuração do runner.
- As regras de negação por padrão de texto podem gerar falsos positivos, por exemplo se a mensagem do commit contiver literalmente `--amend` ou `--force`.
- A exigência de caminhos explícitos no `git add` é intencional e menos prática que `git add .`.
- A análise é local e limitada ao que estiver disponível no repositório; a skill não acessa rede.

As decisões de segurança e o checklist de revisão estão documentados em [`HARDENING.md`](HARDENING.md).
