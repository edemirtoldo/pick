# PICK - Programa Intensivo em Containers e Kubernetes 2024 :computer: 

## Day-1 Docker :rocket:

## Containers, Namespaces, Cgroups e Docker

### O que são containers?
São ambientes isolados que compartilham o mesmo kernel do sistema operacional.

### O que são namespaces?
São mecanismos de isolamento de recursos do sistema operacional.

### O que são cgroups?
São mecanismos de limitação de recursos do sistema operacional.

### O que é o Docker?
É uma ferramenta que permite a criação e o gerenciamento de containers.

### O que é o Docker Hub?
É um repositório de imagens Docker.

### Instalando o Docker

```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Comandos básicos do Docker:

```
# Executa um container em modo interativo com porta 80 mapeada para a porta 8080 do host
docker container run -it -p 8080:80 --name meu-ngnix nginx

# Lista os containers em execução
docker container ls

# Lista as imagens
docker image ls

# Criar um container sem executar
docker container create -it --name meu-nginx nginx

# Para um container
docker container stop meu-nginx

# Inicia um container
docker container start meu-nginx

# Remove um container
docker container rm meu-nginx

# Remove uma imagem
docker image rm nginx

# Conectar em um container em execução
docker container attach meu-nginx

# Visualizar os logs de um container
docker container logs meu-nginx

# Visualizar o consumo de recursos de um container
docker container stats meu-nginx

# Criar uma imagem a partir de um Dockerfile
docker image build -t meu-nginx:1.0 .
```

