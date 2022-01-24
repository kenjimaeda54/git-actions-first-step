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
  - Actions sao tarefas que voce pode adicionar para criar um job e costumizar seu workflow
  - Github tem um maktplace dos actions oficaiis ideal
  
  
  
  
  
  
  
  
