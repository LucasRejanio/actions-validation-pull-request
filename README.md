<img alt="NodeJS" src="https://img.shields.io/badge/node.js%20-%2343853D.svg?&style=for-the-badge&logo=node.js&logoColor=white"/> <img alt="GitHub Actions" src="https://img.shields.io/badge/github%20actions%20-%232671E5.svg?&style=for-the-badge&logo=github%20actions&logoColor=white"/>

[![build](https://img.shields.io/wercker/build/wercker/go-wercker-api.svg)](https://github.com/LucasRejanio/apply-label-pr-actions/actions)

# Apply label
Esse Action adiciona uma label chamada **Release** para todas as pull requests que usam uma branch com o prefixo 'next-release-' no nome. 

```yml
name: Apply release label in pull requests 

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
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            github.issues.addLabels({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              labels: ['release']
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
              labels: ['release']
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
