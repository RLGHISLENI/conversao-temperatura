# Desafio 01 - Docker

## Pré requisitos

- Linux
- Terminal
- VSCode
- Docker
- Git
- ASDF
- kubectl
- [kubectx](https://github.com/ahmetb/kubectx)
- k3d

## Instalação de pré requisitos

Para este desafio utilizaremos o **k3d** para criação de nosso cluster kubernetes e o **kubectl** para criação de pods, replicasets, services e deployments.

Primiero vamos instalar o **asdf** para podermos controlar todas as versões de pacotes instalados em nossa distribuição linux, permitindo a coexistência de mais de uma versão da mesma ferramenta em nosso sistema operacional.

### **Instalando o asdf**

Abra o terminal na pasta raiz do seu usuário e clone o repositório do asdf

```zsh
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.9.0
```

Abra e edite o arquivo `.bashrc` com permissão de administrador utilizando o editor `nano`

```zsh
sudo nano ~/.bashrc
```

Adicione a instrução abaixo na última linha do arquivo, salve e feche

```zsh
. $HOME/.asdf/asdf.sh
```

Execute o comando abaixo para validar a instalação do asdf

```zsh
asdf info
```

Caso não funcione, reinicie seu terminal e tente novamente.

### **Instalando o k3d e kubectl/kubectx**

Após a instalação do asdf, abra o terminal e execute a lista de comandos abaixo:

**k3d:**
```zsh
asdf plugin-add k3d && asdf install k3d latest 
```

**kubectl/kubectx:**
```zsh
asdf plugin-add kubectl && asdf install kubectl latest && asdf plugin-add kubectx && asdf install kubectx latest
```

Agora para saber se **k3d** e o **kubectl** foram instalados corretamente pelo `asdf`, execute os comandos abaixo para identificar a versão instalada e defini-la como `global` para poder ser utilizada a partir de qualquer projeto.

```zsh
# Lista todas ferramentas instaladas pelo asdf
asdf list
```

Identifique a versão de cada ferramenta instalada e defina ela como global em seu sistema operacional conforme demosntrado abaixo:

```zsh
# Define cada versão a ser utilizada como global 
asdf global k3d 5.4.1
asdf global kubectl 1.23.5
asdf global kubectx 0.9.4
```

Veja o resultado executando o comando que demosntra as versões instaladas e definidas pelo `asdf` em seu sistema operacional.

```zsh
# Lista qual a versão definida para cada ferramenta
asdf current
```

Estes comandos combinados irão realizar a **adição do plugin** k3d/kubectl ao controlador asdf, **intalação** no sistema operacional e definição do escopo de utilização da **versão instalada como Global**

> **OPCIONAL: Instale o [fzf](https://github.com/junegunn/fzf) (command-line fuzzy finder)**
>
> _O **`fzf`** é um filtro interativo do Unix para linha de comando que pode ser usado com qualquer lista; arquivos, histórico de comandos, processos, nomes de host, favoritos e etc_.

Para instalar este recurso em seu sistema operacional linux, execute os passos descritos abaixo:

```zsh
# Instala e configura o bash para utizar o zfz
sudo apt-get install fzf

# Reinicia o bash para aplicar a instalação
source ~/.bashrc
```

## Alternar entre contextos (clusters)

Quando temos mais de um ambiente configurado com o cluster kubernetes sentimos a necessidade de alternar entre eles a partir do terminal, e para realizar esta operção utilizamos o `kubectx`.

Veja como utiliza-lo abaixo:

```zsh
# Lista todos os contextos 
kubectx

# Troca para o contexto definido
kubectx <context-name>

# Exibe todos os objetos do contexto definido
kubectl get all
```

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
docker run -d -p 8080:8080 --name=conversao-temperatura rlghisleni/conversao-temperatura:latest
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

Realize o login na conta do DockerHub, informando o usuário e senha para se autenticar na plataforma.

```zsh
docker login
```

Envie a nova versão da imagem alterada para o Service Registry

```zsh
docker push rlghisleni/conversao-temperatura:v[n]
```

Envie também a imagem **latest** atualizada

```zsh
docker push rlghisleni/conversao-temperatura:latest
```

## Como rodar o projeto no Kubernetes

Utilizando k3d crie um cluster kubernetes conforme o código abaixo:

```zsh
k3d cluster create meucluster --agents 1 --servers 1 -p "8080:30000@loadbalancer"
```

Navegue até a pasta `k8s` do projeto e usando o `kubectl` execute o manifesto de deployment

```zsh
kubectl apply -f deployment.yaml
```

Pronto, sua aplicação está no ar utilizando todo o poder dos containers docker e sendo orquestrada pelo kubernetes.

Abra o navegador de sua preferência na url _**http://localhost:8080/**_ e o projeto será apresentado.


## Bônus 01 - Executando containers com Azure App Service

- [Repositório no Docker Hub contendo a imagem do Projeto](https://hub.docker.com/r/rlghisleni/conversao-temperatura/)

- [Projeto executando no Azure App Service](https://iniciativa-kubernetes-web.azurewebsites.net/)

## **Informações Importantes**

### **Benefícios no uso de containers**
  
Um dos objetivos é facilitar o desenvolvimento, acelerar a implantação e a execução de aplicações em _**ambientes isolados**_.

Com o uso de containers podemos ter portabilidade, agilidade, escalabilidade, controle e isolamento em todo o fluxo entre o Desenvolvimento e Operações.

A utilização de containers torna possível redução de custos e permite que tenhamos o mesmo ambiente de produção rodando em nossa máquina de desenvolvimento, além de permitir que tenhamos _**diversos ambientes de programação diferentes na mesma máquina**_ sem conflito entre eles.

Com os containers temos uma _**redução no uso de VMs**_ e consequentemente uma redução no uso de memória, pois uma aplicação rodando em container será bem mais leve que uma aplicação rodando em uma VM completa, visto que o container não depende de um sistema operacional completo mas sim do mínimo necessário para executar a aplicação.

Outra vantagem é a possibilidade _**guardar uma imagem**_ completa do ambiente e disponibilizar através de um Service Restry para toda equipe, além de ser possível gerar uma imagem do ambiente com a aplicação configurada e pronta para ser executada.

O uso de containers facilita muito a adoção de processos de _**automação via deploy**_ (CI/CD).

### **Qual a diferença entre máquinas virtuais e containers e quais são as vantagens da containerização?**

Os containers são bem mais leves que as VMs que são máquinas completas e podem ser personalizados com o mínimo necessário para sua execução, possibilitando assim um melhor escalonamento e _**diminuindo custos desnecessários**_ com infraestrutura. 

O tempo de inicialização de um container é bem mais rápido que o de uma VM e isso pode fazer toda diferença na execução da aplicação em produção, principalmente falando em utilização de micro serviços.

### **Boas práticas na construção de imagens**

Preferir o padrão de nomenclatura utilizando namespace na criação de imagens, adicionar a versão no nome e criar sempre uma imagem latest para manter a última versão atualizada.

_**Sempre utilizar imagens oficiais**_ como base para nossas imagens.
Sempre especificar a versão na tag da imagem para evitar problemas de atualização.

Não utilizar mais de um processo para cada imagem docker, a menos que seja necessário.

Utilizar as camadas na construção do arquivo Dockerfile para evitar processamento desnecessário.

Sempre _**utilizar o arquivo .dockerignore**_ para não considerar arquivos ou pastas na construção da imagem.

Utilizar sempre o _**COPY ao invés do ADD**_, a menos que seja necessário.

Atenção ao utilizar os comandos ENTRYPOINT e CMD, caso necessário poderão ser utilizados os dois em conjunto.

Utilizar imagens _**multistage build para linguagens de programação compiladas**_ e/ou JIT reduzindo o tamanho final da imagem.

Sempre armazenar as imagens e suas versões em um Service Registry.

## Referências

- [Meu Registry Server - DockerHub](https://hub.docker.com/u/rlghisleni)
- [Exemplos básicos de pod, replicaset e deployment](https://github.com/RLGHISLENI/iniciativa-kubernetes-manifest)
- [Desafio 02 e 03 - Kubernetes e Pipeline CI/CD](https://github.com/RLGHISLENI/rotten-potatoes)
