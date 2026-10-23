# Resumo das motivações

O resumo traduz, em linguagem simples, o que foi alterado e por quê — para que o usuário entenda a sugestão de relance, sem ler o diff. Ele acompanha a mensagem de commit sugerida (ver [commit-convention.md](commit-convention.md)).

## Formato

- Lista de bullets iniciados por `- `, no máximo uma linha por bullet.
- Uma mudança ou motivação por bullet; nada de detalhes internos irrelevantes.
- Os bullets são **linhas consecutivas — sem nenhuma linha em branco entre eles**, tanto no resumo quanto no corpo do commit e na lista `Arquivos:` (a única linha em branco do corpo é a que separa o cabeçalho do primeiro bullet).
- Cada commit traz uma linha `Arquivos:` ao final da sua mensagem (depois do corpo, quando houver), com os caminhos que fazem parte dele, separados por vírgula (quebre em várias linhas sob o mesmo rótulo quando forem muitos). É essa lista que diz o que entra em cada `git add`.
- Linguagem simples e direta, sem jargão desnecessário; frases curtas costumam começar pelo que mudou (`Adicionado...`, `Corrigido...`, `Atualizado...`, `Removido...`).
- Na língua da conversa (nesta skill: português) — a mensagem de commit segue o padrão do projeto, o resumo é para quem está lendo a sugestão.
- Não copie o corpo do commit literalmente: o corpo explica o que/como técnicos, o resumo diz em palavras simples o que mudou e motiva a escolha.

Errado — pulando linha entre os bullets do resumo (não faça isso):

```text
Resumo:
- Criada a recuperação de senha por e-mail com link temporário

- Limitado o número de solicitações para evitar abuso
```

Certo: os bullets seguidos, um abaixo do outro, como em todos os exemplos abaixo.

## Com múltiplos commits

Quando a sugestão tem vários commits atômicos, entregue **um único resumo**, com um bullet por mudança, após as mensagens. Cada bullet deve corresponder ao que mudou em um dos commits sugeridos.

E cada commit vem com a sua linha `Arquivos:`: é ela que diz quais arquivos entram em cada `git add`/commit — sem ela, não dá para executar a sugestão. Arquivos que não aparecem em nenhum commit não fazem parte da sugestão; informe-os como pendentes, se houver.

## Com arquivos staged

Se houver arquivos staged, a apresentação começa com a frase exata exigida pela regra de staged da SKILL; a mensagem vem com a linha `Arquivos:` listando exatamente o que está staged, e o resumo fecha a apresentação:

> Considerando os arquivos já em staged o commit que eu sugiro é ...

## Exemplos

### 1. Commit único com arquivos staged

```text
Considerando os arquivos já em staged o commit que eu sugiro é ...

feat(auth): add password recovery flow

- Add a recovery endpoint that emails a token valid for 30 minutes
- Limit recovery requests to five per minute per account
- Add the reset form to the login screen

Arquivos: src/auth/recovery.ts, src/auth/recovery.routes.ts, src/pages/ResetPassword.tsx

Resumo:
- Criada a recuperação de senha por e-mail com link temporário
- Limitado o número de solicitações para evitar abuso
- Adicionado o formulário de nova senha na tela de login
```

### 2. Nada staged: separação em commits atômicos

```text
Sugestão de commits atômicos:

1. feat(ui): add responsive navigation

   - Add a collapsible menu for mobile devices
   - Improve keyboard navigation and accessibility

   Arquivos: src/components/Navbar.tsx, src/hooks/useMediaQuery.ts, src/styles/nav.css

2. fix(auth): handle expired access tokens

   - Attempt to refresh the token when a refresh token is available
   - Redirect to the login page when the session cannot be restored

   Arquivos: src/auth/token.ts, src/auth/session.ts

3. style(formatting): format touched files

   - Apply the project formatter to files changed in this session

   Arquivos: src/pages/Home.tsx, src/utils/date.ts

Resumo:
- Menu de navegação que se adapta a telas menores
- Corrigida a expiração de sessão que derrubava o usuário no meio do uso
- Código formatado pelo padrão do projeto, sem mudança de comportamento
```

### 3. Alteração simples, só cabeçalho

```text
docs(readme): fix broken install link

Arquivos: README.md
```

```text
Resumo:
- Corrigido o link de instalação quebrado no README
```

### 4. Duas intenções no mesmo conjunto de mudanças

```text
Sugestão de commits atômicos:

1. feat(api): add webhook retries

   - Retry failed deliveries up to three times with backoff

   Arquivos: src/webhooks/retry.ts, src/webhooks/delivery.ts

2. docs(guides): document webhook retry policy

   - Add the retry limits and timeout behavior to the integration guide

   Arquivos: docs/integrations.md

Resumo:
- Entrega de webhooks agora tenta novamente até três vezes antes de falhar
- Guia atualizado com o novo comportamento de retentativa
```

## Regras finais

- Um bullet por mudança ou motivação concreta.
- Nenhum commit sem a sua linha `Arquivos:` — mensagem sem lista de arquivos não é executável.
- Prefira dizer o efeito para o usuário (`Recuperação de senha por e-mail`) a descrever o mecanismo interno (`Novo controller com injeção de dependência`), quando os dois existirem.
- Sem bullets vazios, sem repetir a descrição do cabeçalho sem acrescentar informação.
- Se uma mudança não agregar ao entendimento (ex.: remoção de arquivo morto), ela pode ficar de fora do resumo — o commit continua sendo sugerido normalmente.
