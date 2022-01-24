# Git actions WorkFlow
Praticando git actions 

## Motivação
Praticar git actions é suas ferramentas, para poder aplicar em projetos e trabalhos </br>
[docs](https://docs.github.com/en/developers/apps)

## Feture
- Para debugar seu git actions, github fornece uma solução que chama [debug](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/enabling-debug-logging)
- Ao ativar o debug nas configurações do seu perfil, os arquivos yml irão detalhar passo a passo cada comando
- Para cada arquivo yml você pode especificar um nome no topo, ele e opcional, mas para cada job precisa possuir nome
- Exemplo abaixo o nome do job e run-shell-ubuntu
- Apos o pipe estou fazendo multi linhas e um recurso do [yml](https://github.com/kenjimaeda54/yaml-feature) que esta disponível no github actions, poderia também escrever run:pwd,run:node version
- Quando desejo trabalhar com outro sistema operacional precisa estar em outro job, exemplo abaixo estou usando windows
- Por padrão os jobs rodam em paralelo com needs, eles se tornam sequenciais

``` yml
  
name: Work Flow Shell

on: [pull_request]

jobs:
  run-shell-ubuntu: 
    runs-on: ubuntu-latest
    steps:
      - name: echo string
        run: echo "Hello World"
      - name: multiline
        run: |
          pwd
          node --version
      - name: shell with python
        run: |
          import platform
          print(platform.processor())
        shell: python
  run-shell-window: 
    runs-on: windows-latest
    needs: ["run-shell-ubuntu"] 
    steps:
      - name: use command windows
        run: GET-LOCATION
      - name: use equal commands on bash 
        run: pwd
        shell: bash
 
  ```
  
  
  ##
  
  - Git hub fornece [actions](https://docs.github.com/en/actions/creating-actions/about-custom-actions)
  - Actions são tarefas que você pode adicionar para criar um job e customizar seu workflow
  - Github tem um [marktplace](https://github.com/marketplace?type=actions) dos actions oficias
  - Com github actions você consegue disparar [enventos](https://docs.github.com/en/rest/reference/repos#create-a-repository-dispatch-event) a partir de uma API rest, usando o post e url do github
  - Abaixo estou usando um action para o github visualizar meus arquivos locais e não da máquina virtual [actions/checkout](https://github.com/actions/checkout)
  - Abaixo tem outro exemplo de [actions](https://github.com/actions/hello-world-javascript-action), que e um simples hello em javascript
  - Para usar os actions precisa ir ate o repositorio quem criou e olhar as recomendações
  - E possível configurar agendas para que seus workflows sejam executados em determinados momentos
  - Agendas usam o modelo [cron](#https://crontab.guru/), algo disponível também no linux
  - Menor tempo possível  para determinar  cada execução e 5 minutos
  - Normalmente as configurações de agenda ficam no topo
  - Para realizar os eventos precisa de uma autenticação básica, usando token disponível no seu perfil
  - Esta e uma url valida https://api.github.com/repos/{nomde do usuário do repo}/{nome do repo}/dispatches
  - No meu exemplo uma api  precisa recurso post no formato json com `event_type:build`
  - Tambem e possivel passar um palyoud
  - Job abaixo e só executado quando realizar o post

``` yml

schedule: 
   - cron: "*/20 * * * *"
   - cron: "*/23 * * * *"
 
on:
  repository_dispatch:
    types:
      - build
  pull_request:
    types:
      - assigned
      - closed
      - opened
      - reopened


```

## 

- Gihub acitons possui também suas [variáveis](https://docs.github.com/en/actions/learn-github-actions/environment-variables) de ambiente
- Por padrão github injeta em cada repositório um token que e possível pegar com [github_token](https://docs.github.com/en/actions/security-guides/automatic-token-authentication)
- Abaixo fiz um exemplo criptografando um arquivo e fazendo sua descriptografia
- No perfil do usuário você pode visualizar [secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets), são variáveis que não ambiente, mas o github consegue compreender, para acessar elas no yml precisa usar screts.NomeVoceEscreveu 
- As variaveis secretes suportam no máximo 64 kb, maneira de mandar arquivos maiores e criptografar no diretório local do projeto é guardar a chave que faz a descriptografia no secrets
- Ferramenta que usei para fazer a [criptografia](# https://www.gnupg.org/)
- Docs detalhado  [criptografia](#https://www.gnupg.org/documentation/manpage.html)  
- Não esquece de colocar checkout para acessar a máquina local
  
``` yml
  
  name: Environment git hub on

on: pull_request

env:
  WF_ENV: Available all jobs
  # em git fetch depois,git switch master e git push -u origin, e que o repositorio para o boot  inicia vazio
  # poderíamos configurar qualquer usuário ali
jobs:
  
  # https://elias.praciano.com/2018/01/criptografia-com-o-gnupg-para-iniciantes/
  decrypt:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Decrypt file
        run:
          # isso tudo e um comando entao nao coloca pipe
          gpg --decrypt --batch --yes --quiet --passphrase="$SECRET" --output $HOME/api_secret.json
          api_secret.json.gpg
        env:
          SECRET: ${{ secrets.API_KEY }}
      - name: Get credentials - name
        run: |
          cat $HOME/api_secret.json
          ls -a
          git switch develop
          git pull
  long-env:
    runs-on: ubuntu-latest
    env:
      JOB_ENV: Available in job
    steps:
      - name: Send random text
        run: |
          pwd  
          ls -a
          git init
          git remote add origin "https://$GITHUB_ACTOR:${{ secrets.GITHUB_TOKEN }}@github.com/$GITHUB_REPOSITORY.git"
          git config --global user.email "bot@email.com"
          git config --global user.name  "boot"
          ls -a 
          pwd
          git fetch
          git switch develop                       
          git push -u origin develop
          git pull
          echo $RANDOM >> random.txt
          git add .
          git commit -m "boot added number random"
          git push -u origin develop
      - name: List environment
        env:
          STEP_ENV: Available in steep
        run: |
          echo Environment global "$WF_ENV"
          echo Environment job "$JOB_ENV"
          echo Environment step "$STEP_ENV"

  short-env:
    runs-on: ubuntu-latest
    steps:
      - name: List environment git hub
        run: |
          echo Environment global "$WF_ENV"
          echo Name of job $GITHUB_JOB
          echo Id commit  $GITHUB_SHA

  
  
 
  ```
  
  
  ##
  
  
  
  
  
