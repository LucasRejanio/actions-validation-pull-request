![Test library workflow status](https://github.com/tcort/markdown-link-check/workflows/Test%20library/badge.svg)

# Apply label
Esse Action adiciona uma label para todas as pull requests que usam uma branch com o prefixo 'next-release-' no nome. 

```yml
name: Apply label in pull requests 

on:
  pull_request:
    types: [opened]

jobs:
  apply-label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v3
        if: contains(github.head_ref, 'next-release-')
        with:
          github-token: ${{secrets.GITHUB_TOKEN}}
          script: |
            github.issues.addLabels({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              labels: ['Release']
            })
```

## Explicação
A condicional está baseada no nome da branch de origem, portanto, estamo utlizando o parâmetro **github.head_ref**. Se precisarmos verificar o nome da branch de destino poderiamos usar **github.ref**.

#### Condicional IF

Verificar o nome fixo de uma branch: 
```yml
if: {{ github.ref == 'release' }}
```

Verificar o nome dinâmico que obtem um prefixo de uma branch: 
```yml
if: contains(github.head_ref, 'next-release-')
```
#### Aplicação da label

```yml
script: |
            github.issues.addLabels({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              labels: ['Release']
            })
```

#### Extra
Pegar nome dos pull request por meio do comando curl: 

```console
curl -s -u user:tokengerado https://api.github.com/repos/NOME_DO_DONO_DO_REPOSITORIO/NOME_DO_REPOSITORIO/pulls
```

#### Token

```console
curl -s -u lucasrejanio:token https://api.github.com/repos/lucasrejanio/apply-label/pulls | grep title | cut -d '"' -f 4
```

#### Secret

```console
echo ::set-env name=pull_request::$(curl -s -u lucasrejanio:${{ secrets.ACTIONS_SECURE_SECRET }} https://api.github.com/repos/lucasrejanio/apply-label/pulls | grep title | cut -d '"' -f 4)
```
