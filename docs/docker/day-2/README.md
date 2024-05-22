# PICK - Programa Intensivo em Containers e Kubernetes 2024 :computer: 

## Day-2 Docker :rocket:

## Dockerfile, Entrypoint e Healthcheck

### O que é um Dockerfile?

É um arquivo de texto que contém todos os comandos necessários para criar uma imagem Docker.

### O que é um Entrypoint?

É um comando que é executado quando o container é iniciado.

### O que é um Healthcheck?

É um comando que verifica se o container está saudável.

### Criando o primeiro Dockerfile

Criando diretorio 

```bash
mkdir Dockerfile
cd Dockerfile
```
Arquivo `Dockerfile` deve ser sempre utilizado 

```dockerfile
FROM ubuntu:18.04
RUN apt-get update && apt-get install nginx -y
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"] 
```

### Explicação do Dockerfile

**Linha 1:** `FROM ubuntu:18.04`

**Descrição:** Esta linha especifica a imagem base para o contêiner Docker. Neste caso, é a imagem do Ubuntu na versão 18.04. A imagem base é o ponto de partida para construir o contêiner, fornecendo o sistema operacional e suas bibliotecas essenciais.

**Linha 2:** `RUN apt-get update && apt-get install nginx -y`

**Descrição:** Este comando executa duas operações em uma única linha:

- `apt-get update`: Atualiza a lista de pacotes disponíveis e suas versões. Isso é importante para garantir que você está instalando a versão mais recente dos pacotes.

- `apt-get install nginx -y`: Instala o servidor web Nginx sem solicitar confirmação do usuário devido ao -y. O Nginx é um servidor HTTP de código aberto que pode ser usado para servir páginas web.

**Nota:** Usar && combina esses comandos, garantindo que a instalação do Nginx só aconteça se a atualização da lista de pacotes for bem-sucedida.

**Linha 3:** `EXPOSE 80`

**Descrição:** Este comando informa ao Docker que o contêiner vai escutar na porta 80 em tempo de execução. A porta 80 é a porta padrão para HTTP, o que significa que o Nginx estará servindo conteúdo nessa porta.

**Nota:** Este comando não publica a porta para o host, mas é uma documentação para os usuários do contêiner e ferramentas que usam essa informação para mapeamento de portas.

**Linha 4:** `CMD ["nginx", "-g", "daemon off;"]`

**Descrição:** Define o comando padrão que será executado quando o contêiner iniciar.

- `nginx`: Inicia o servidor Nginx.

- `-g "daemon off;"`: Esta opção diz ao Nginx para rodar em primeiro plano (foreground) em vez de se tornar um processo daemon (em segundo plano). Isso é importante para contêineres Docker, porque o contêiner precisa de um processo em primeiro plano para permanecer em execução.

**Resumo**

Este Dockerfile cria um contêiner baseado no Ubuntu 18.04, atualiza a lista de pacotes, instala o Nginx, expõe a porta 80 e configura o Nginx para rodar em primeiro plano. Com esse contêiner, você terá um servidor web Nginx pronto para servir conteúdo na porta 80.

### Vamos executar o docker build 

O comando docker build é usado para construir uma imagem Docker a partir de um Dockerfile. Aqui está uma descrição detalhada de como ele funciona e alguns dos seus principais aspectos:

Comando Básico
bash
Copiar código

```bash
docker build [OPTIONS] PATH | URL | -
```

**Descrição**

- **Construção da Imagem:* O comando `docker build` lê as instruções contidas no Dockerfile (localizado no diretório especificado pelo `PATH`) e monta uma nova imagem Docker com base nessas instruções.

- **PATH | URL | -:**

    - `PATH`: Especifica o caminho para o diretório que contém o Dockerfile.
    - `URL`: Pode apontar para um repositório remoto contendo o Dockerfile.
    - `-`: Lê o Dockerfile do input padrão (stdin).

**Principais Opções**
- `-t, --tag`: Nomeia e marca a imagem criada. Pode ser usada várias vezes para aplicar várias tags à imagem.

```bash
docker build -t myimage:latest .
```

- `-f, --file`: Permite especificar um Dockerfile que não esteja no diretório padrão.

```bash
docker build -f /path/to/Dockerfile .
```

- `--build-arg`: Define variáveis de ambiente para a construção da imagem. Essas variáveis podem ser usadas dentro do Dockerfile.

```bash
docker build --build-arg HTTP_PROXY=http://proxy.example.com .
```

- `--no-cache`: Não utiliza a cache ao criar a imagem, garantindo que cada instrução do Dockerfile seja executada novamente.

```bash
docker build --no-cache -t myimage:latest .
```





----

```dockerfile
# Define a imagem base
FROM ubuntu:18.04

# Define o mantenedor da imagem
LABEL maintainer="edemirtoldo@gmail.com"

# Executa um comando
RUN apt-get update && apt-get install -y nginx curl

# Define a porta que o container irá expor
EXPOSE 80

# Define o diretório de trabalho
WORKDIR /var/www/html

# Adiciona arquivos para o container
ADD node_exporter-1.6.0.linux-amd64.tar.gz /root/node-exporter

# Copia um arquivo do host para o container (Não esquecer de criar o arquivo index.html)
COPY index.html .

# Adiciona uma variável de ambiente
ENV APP_VERSION 1.0.0

# Exectando o Entrypoint
ENTRYPOINT ["nginx"]

# Define o comando que será executado quando o container for iniciado
CMD ["-g", "daemon off;"]

# Exectando o Healthcheck
HEALTHCHECK --interval=5s --timeout=3s CMD curl -f http://localhost/ || exit 1
```

### Glossário de Dockerfile

- **ADD:** Instrução utilizada no Dockerfile para copiar arquivos e diretórios para dentro do container.

- **CMD:** Instrução utilizada no Dockerfile para especificar o comando padrão a ser executado quando o container for iniciado.

- **COPY:** Instrução utilizada no Dockerfile para copiar arquivos e diretórios para dentro do container.

- **ENTRYPOINT:** Instrução utilizada no Dockerfile para especificar o comando padrão a ser executado quando o container for iniciado, porém não pode ser substituído ao iniciar o container.

- **ENV:** Instrução utilizada no Dockerfile para definir variáveis de ambiente que serão utilizadas durante a construção e execução do container.

- **EXPOSE:** Instrução utilizada no Dockerfile para definir as portas em que o container irá escutar, permitindo a comunicação com outros containers ou com o host.

- **FROM:** Instrução utilizada no Dockerfile para especificar a imagem base que será utilizada para construir o container.

- **LABEL:** Instrução utilizada no Dockerfile para adicionar metadados à imagem, como informações do autor e descrição.

- **MAINTAINER:** Instrução utilizada no Dockerfile para especificar o autor da imagem.

- **RUN:** Instrução utilizada no Dockerfile para executar comandos durante a construção do container.

- **USER:** Instrução utilizada no Dockerfile para especificar o usuário que será usado para executar os comandos dentro do container.

- **VOLUME:** Instrução utilizada no Dockerfile para definir um ponto de montagem de volume, permitindo o compartilhamento de dados entre o host e o container.

- **WORKDIR:** Instrução utilizada no Dockerfile para definir o diretório de trabalho dentro do container, onde os comandos serão executados.

Lembre-se de sempre consultar a documentação oficial do Docker para obter informações completas sobre cada termo.