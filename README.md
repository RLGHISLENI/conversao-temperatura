# Desafio 01 - Docker

## Pré requisitos

- Linux
- Terminal
- VSCode
- Docker
- Git

## Iniciar o Docker na estação

Verifique o status do docker para saber se ele já está rodando na máquina.

```zsh
sudo service docker status
```

Caso não esteja rodando, inicie o docker conforme abaixo:

```zsh
sudo service docker start
```

## Como baixar e executar a imagem docker do projeto em sua máquina

Abra o terminal de comandos e execute o comando abaixo:

```zsh
docker run -d -p 8080:8080 rlghisleni/conversao-temperatura:latest
```

Liste os containers em execução e verifique se _**rlghisleni/conversao-temperatura**_ consta na relação.

```zsh
docker container ls
```

Abra o navegador de sua preferência na url _**http://localhost:8080/**_ e o projeto será apresentado.

## Como baixar o projeto em sua máquina e realizar build na imagem docker

Abra o terminal e clone este repositório público utilizando o git

```zsh
git clone https://github.com/RLGHISLENI/conversao-temperatura.git
```

Navegue até a pasta fonte do projeto

```zsh
cd conversao-temperatura/src
```

Abra o VSCode para poder editar os arquivos Dockerfile e .dockerignore

```zsh
code .
```

Realize as alterações necessárias no projeto e nos arquivos docker e salve suas alterações.

Realize o build para atualizar a imagem docker.

> ATENÇÃO: Incremente a versão denominada :**v[n]**, onde **[n]** é o número da versão atual mais 1.
> Por exemplo _**:v2**_

```zsh
docker image build -t rlghisleni/conversao-temperatura:v[n] .
```

Atualize a versão _**:latest**_ da imagem

```zsh
docker tag rlghisleni/conversao-temperatura:v[n] rlghisleni/conversao-temperatura:latest
```

Liste as imagens para visualizar a nova versão da imagem criada

```zsh
docker images
```

## Como atualizar a imagem no Registry DockerHub

Realize o login na conta do DockerHub, informando o usuário e senha para se autenticar a plataforma.

```zsh
docker login
```

Envie a nova versão da imagem alterada para o servidor Registry

```zsh
docker push rlghisleni/conversao-temperatura:v[n]
```

Envie também a imagem **latest** atualizada

```zsh
docker push rlghisleni/conversao-temperatura:latest
```
