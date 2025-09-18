# Git

## Comandos

### Commits
#### Exemplos de Boas Mensages de Commit

> Material gentilmente cedido por Antonio Carlos de Lima Junior
https://github.com/acnaweb/git

| Tipo             | Exemplo de Mensagem                                        |
|------------------|------------------------------------------------------------|
| **Adição de Funcionalidade** | `feat: add user authentication with JWT`                  |
| **Correção de Bug**         | `fix: correct bug in the email validation logic`            |
| **Refatoração de Código**   | `refactor: simplify user registration logic`                |
| **Documentação**            | `docs: update README with installation instructions`         |
| **Melhoria de Performance** | `perf: optimize image loading on homepage`                  |
| **Testes**                  | `test: add unit tests for user registration module`         |
| **Atualização de Dependências** | `chore: update lodash to version 4.17.21`             |
| **Remoção de Funcionalidade** | `remove: delete deprecated user profile API endpoint`   |
| **Configuração**            | `config: add ESLint configuration for React projects`      |

*** 
## Criando branch

```bash
git checkout -b <branch-name>

git add .

git commit -m "description"

git push origin <branch-name>

git checkout main

git merge <branch-name>

git push origin main
```

### Resumo do Fluxo de Trabalho

1. `main`: ter a versão inicial do seu projeto aqui.

2. `git checkout -b aula01`: cria uma nova branch para a primeira aula, por exemplo.

3. `Trabalhe na aula01`: fazer os `commits` com as alterações da aula.

4. `git push origin aula01`: enviar sua `branch` para o `GitHub`.

5. `git checkout main`: voltar para a `branch main`.

6. `git merge aula01`: fazer o `merge` das alterações da `aula01` na `main`.

7. `git push origin main`: enviar as alterações da `main` para o `GitHub`.

Repetir os passos para a próxima aula, criar uma nova `branch` a partir da main já atualizada (`git checkout -b aula02`, por exemplo), e repitir o processo.