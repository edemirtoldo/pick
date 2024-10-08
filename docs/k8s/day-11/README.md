# PICK - Programa Intensivo em Containers e Kubernetes 2024 :computer:

## Day-11 Kubernetes :rocket:

##  Ingress e Context no Kubernetes 

### O que é o Ingress?

O Ingress é um recurso do Kubernetes que gerencia o acesso externo aos serviços dentro de um cluster. Ele funciona como uma camada de roteamento HTTP/HTTPS, permitindo a definição de regras para direcionar o tráfego externo para diferentes serviços back-end. O Ingress é implementado através de um controlador de Ingress, que pode ser alimentado por várias soluções, como NGINX, Traefik ou Istio, para citar alguns.

Tecnicamente, o Ingress atua como uma abstração de regras de roteamento de alto nível que são interpretadas e aplicadas pelo controlador de Ingress. Ele permite recursos avançados como balanceamento de carga, SSL/TLS, redirecionamento, reescrita de URL, entre outros.

**Principais Componentes e Funcionalidades:**

**Controlador de Ingress:** É a implementação real que satisfaz um recurso Ingress. Ele pode ser implementado através de várias soluções de proxy reverso, como NGINX ou HAProxy.

**Regras de Roteamento:** Definidas em um objeto YAML, essas regras determinam como as requisições externas devem ser encaminhadas aos serviços internos.

**Backend Padrão:** Um serviço de fallback para onde as requisições são encaminhadas se nenhuma regra de roteamento for correspondida.

**Balanceamento de Carga:** Distribuição automática de tráfego entre múltiplos pods de um serviço.

**Terminação SSL/TLS:** O Ingress permite a configuração de certificados SSL/TLS para a terminação de criptografia no ponto de entrada do cluster.

**Anexos de Recurso:** Possibilidade de anexar recursos adicionais como ConfigMaps ou Secrets, que podem ser utilizados para configurar comportamentos adicionais como autenticação básica, listas de controle de acesso etc.


### Configurando o Kind para usar o Ingress

Por padrão, o Kind não vem com o Ingress habilitado. Para habilitá-lo, precisamos criar um arquivo de configuração do Kind com o seguinte conteúdo:

`kind-com-ingress.yaml`

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
```

O arquivo de configuração acima habilita o Ingress e mapeia as portas 80 e 443 do host para as portas 80 e 443 do container.

Para criar o cluster com o arquivo de configuração acima, execute o seguinte comando:

```bash
kind create cluster --config kind-com-ingress.yaml
```

### Instalando o [Ingress NGINX Controller no Kind](https://kind.sigs.k8s.io/docs/user/ingress/#ingress-nginx)

Para instalar o Ingress NGINX Controller, execute o seguinte comando:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

Aguarde a instalação do Ingress NGINX Controller:

```bash
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```

Para verificar se o Ingress foi criado no Kind, execute o seguinte comando:

```bash
 kubectl get pods -n ingress-nginx
```
Será apresentado o seguitne conteudo:

```bash
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-bn7zw        0/1     Completed   0          79s
ingress-nginx-admission-patch-jxzzq         0/1     Completed   0          79s
ingress-nginx-controller-6b7956cff5-n8j2m   1/1     Running     0          79s
```

Para instalar o Ingress NGINX Controller em um cluster que não seja o Kind, você pode seguir as instruções ds [documentação oficial](https://kubernetes.github.io/ingress-nginx/deploy/).

### Criando um Ingress

Para criar um Ingress, precisamos ter um serviço rodando no cluster. Para isso, vamos criar um deployment e um serviço do Giropops Senha + Redis com os seguintes comandos (execute os comandos no diretório `pick/docs/k8s/day-11/files` pois os arquivos de configuração estão nesse diretório):

```bash
kubectl apply -f app-deployment.yaml
kubectl apply -f app-service.yaml
kubectl apply -f redis-deployment.yaml
kubectl apply -f redis-service.yaml
```

#### Agora, vamos criar o Ingress propriamente dito. 

Para isso, vamos criar nosso primeiro arquivo `ingress-1.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: giropops-senhas
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /giropops-senhas
        pathType: Prefix
        backend:
          service:
            name: giropops-senhas
            port:
              number: 5000
```

Vamos fazer o deploy deste ingress.

```bash
kubectl apply -f ingress-1.yaml
```

Vamos consultar o ingress.

```bash
kubectl get ingress
```

Será exibido do seguinte conteudo:

``` bash
NAME              CLASS    HOSTS   ADDRESS     PORTS   AGE
giropops-senhas   <none>   *       localhost   80      3m36s
```

Vamos ver detalhes deste ingress.

``` bash
kubectl describe ingress giropops-senhas
```
Será exibido o seguinte conteúdo.

``` bash
Name:             giropops-senhas
Labels:           <none>
Namespace:        default
Address:          localhost
Ingress Class:    <none>
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /giropops-senhas   giropops-senhas:5000 (10.244.0.8:5000,10.244.0.9:5000)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
Events:
  Type    Reason  Age                    From                      Message
  ----    ------  ----                   ----                      -------
  Normal  Sync    7m29s (x2 over 7m32s)  nginx-ingress-controller  Scheduled for sync
```

Verificamos que no Address temos o endereço do nosso endereço do nosso ingress. (`localhost`)

Basta acessar o link: http://localhost/giropops-senhas

Notamos um erro na pagina, algo quebrou:

![ingress-1](https://github.com/edemirtoldo/pick/blob/main/docs/images/ingress-1.png)

Vamos criar outro ingress `ingress-2.yaml`

```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: giropops-senhas-static
spec:
  rules:
  - http:
      paths:
      - path: /static
        pathType: Prefix
        backend:
          service:
            name: giropops-senhas
            port:
              number: 5000
```

Vamos rodar o seguinte comando:

```bash
 kubectl apply -f ingress-2.yaml
```

Vamos consultar os ingress.

```bash
kubectl get ingress
```

Resultado.

```bash
NAME                     CLASS    HOSTS   ADDRESS     PORTS   AGE
giropops-senhas          <none>   *       localhost   80      45m
giropops-senhas-static   <none>   *                   80      20s
```

Vamos acessar novamente o link: http://localhost/giropops-senhas

Notamos que a pagina abriu sem erros.

![ingress-2](https://github.com/edemirtoldo/pick/blob/main/docs/images/ingress-2.png)

Mas ao clicar no botão Gerar da erro.

![ingress-2a](https://github.com/edemirtoldo/pick/blob/main/docs/images/ingress-2a.png)

O erro foi para o backend default do nginx.

Vamos remover os ingress.

```bash
 kubectl get ingress
```

```bash
NAME                     CLASS    HOSTS   ADDRESS     PORTS   AGE
giropops-senhas          <none>   *       localhost   80      83m
giropops-senhas-static   <none>   *       localhost   80      16m
```

Remover o ingress `giropops-senhas`

```bash
kubectl delete ingress giropops-senhas
```

Remover o ingress `giropops-senhas-static`

```bash
kubectl delete ingress giropops-senhas-static
```

Para resolver o problema vamos criar um novo ingress `ingress-3.yaml`

```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: giropops-senhas
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: giropops-senhas
            port:
              number: 5000
```

Vamos rodar o seguinte comando.

```bash
kubectl apply -f ingress-3.yaml
```

Vamos consultar o ingress.

```bash
kubectl get ingress
```

Resultado.

```bash
NAME              CLASS    HOSTS   ADDRESS     PORTS   AGE
giropops-senhas   <none>   *       localhost   80      70s
```

Agora vamos testar o acesso a aplicação, mas vamos direto com o `localhost`

Acesse o link: http://localhost/

Será apresentado a tela corretamente.

![ingress-3](https://github.com/edemirtoldo/pick/blob/main/docs/images/ingress-3.png)

### Caso você tenha mais de um site.

### Criando multiplos Ingress no mesmo Ingress Controller

Vamos criar um pod do nginx manualmente para simular mais um site.

```bash
kubectl run nginx --image nginx --port 80
```

Consultar o pod criado

```bash
kubectl get pods
```

Vamos criar o serviço.

```bash
kubectl expose pod nginx
```

Vamos consultar o service criado.

```bash
kubectl get service
```

Agora vamos criar mais um ingress para este pod nginx criado. `ìngress-4.yaml`

```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: giropops.nginx.io
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80
```

Vamos rodar o seguinte comando.

```bash
kubectl apply -f ingress-4.yaml
```

Consultar os ingress.

```bash
kubectl get ingress
NAME              CLASS    HOSTS               ADDRESS     PORTS   AGE
giropops-senhas   <none>   *                   localhost   80      38m
nginx             <none>   giropops.nginx.io               80      13s
```

Agora precisamos definir um apelido no hosts da maquina.

```bash
sudo vim /etc/hosts
```

Incluir a linha

```bash
127.0.0.1       giropops.nginx.io
```

Vamos acessar o seguinte link: http://giropops.nginx.io/

![ingress-4](https://github.com/edemirtoldo/pick/blob/main/docs/images/ingress-4.png)

Conseguimos acessar diretamente a url `ǵiropops.nginx.io`

Vamos incluir no hosts um novo alias para 'giropops-senhas.io` no hosts 

```bash
sudo vim /etc/hosts
```

incluindo o seguinte conteúdo.

```bash
127.0.0.1       giropops-senhas.io
```

Vamos criar um novo ingress para essa regra `ingress-5.yaml`

```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: giropops-senhas
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: giropops-senhas.io
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: giropops-senhas
            port:
              number: 5000
```

Vamos rodar o seguinte comando.

```bash
kubectl apply -f ingress-5.yaml
```

Vamos consultar o ingress.

```bash
kubectl get ingress
```

Resultado.

```bash
NAME              CLASS    HOSTS                ADDRESS     PORTS   AGE
giropops-senhas   <none>   giropops-senhas.io   localhost   80      60m
nginx             <none>   giropops.nginx.io    localhost   80      22m
```

Agora vamos acessar o seguinte link: http://giropops-senhas.io/

![ingress-5](https://github.com/edemirtoldo/pick/blob/main/docs/images/ingress-5.png)

# Instalando um cluster EKS na AWS para os nossos testes com ingress

Comando para criar um cluster EKS na AWS. 

```bash
eksctl create cluster --name=eks-cluster --version=1.24 --region=us-east-1 --nodegroup-name=eks-cluster-nodegroup --node-type=t3.medium --nodes=2 --nodes-min=1 --nodes-max=3 --managed
```

#### Componentes do Comando

1. `eksctl create cluster`:

    - `eksctl` é uma ferramenta de linha de comando para simplificar a criação e o gerenciamento de clusters EKS.

    - create cluster é o subcomando que cria um novo cluster EKS.

2. `--name=eks-cluster`:

    - Especifica o nome do cluster a ser criado. Neste caso, o nome é eks-cluster.

3. `--version=1.24`:

    - Define a versão do Kubernetes a ser usada no cluster. Aqui, a versão é 1.24.

4. `--region=us-east-1`:

    - Especifica a região da AWS onde o cluster será criado. Neste caso, a região é us-east-1.

5. `--nodegroup-name=eks-cluster-nodegroup`:

    - Define o nome do grupo de nós (nodegroup) dentro do cluster. O nome aqui é eks-cluster-nodegroup.

6. `--node-type=t3.medium`:

    - Especifica o tipo de instância EC2 a ser usado para os nós do cluster. Aqui, o tipo de instância é t3.medium.

7. `--nodes=2`:

    - Define o número inicial de nós no cluster. Este valor inicial é 2.

8. `--nodes-min=1`:

    - Especifica o número mínimo de nós no grupo de nós. O valor mínimo aqui é 1.

9. `--nodes-max=3`:

    - Define o número máximo de nós no grupo de nós. O valor máximo aqui é 3.

10. `--managed`:

    - Indica que os nós do cluster serão gerenciados pela AWS. Isso significa que a AWS se encarregará de tarefas como a aplicação de patches e upgrades para os nós.

#### Resumo

Este comando cria um cluster Kubernetes gerenciado pela AWS (EKS) na região `us-east-1` com o nome `eks-cluster`. O cluster usará a `versão 1.24` do Kubernetes e terá um grupo de nós (nodegroup) chamado `eks-cluster-nodegroup`. O grupo de nós usará instâncias EC2 do tipo `t3.medium`, começará com `2 nós`, e pode `escalar entre 1 e 3` nós conforme necessário.

> Demora uns 20 minutos para o cluster ser criado.

## Contexts no Kubernetes

Contexts são uma forma de organizar e acessar múltiplos clusters Kubernetes. Eles são úteis quando você precisa acessar mais de um cluster Kubernetes, seja localmente ou em um ambiente de produção.

Vamos listar em qual contexto estamos conectados com seguinte comando:

```bash
kubectl config current-context
```

Para listar os contextos disponíveis, execute o seguinte comando:

```bash
kubectl config get-contexts
```

Para mudar de contexto, execute o seguinte comando:

```bash
kubectl config use-context <context-name>
```

### Instalando o Ingress NGINX Controller no EKS

Para instalar o Ingress NGINX Controller no EKS, precisamos executar os seguintes comandos. Não esqueça de mudar o contexto para o cluster EKS antes de executar os comandos:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/aws/deploy.yaml
```

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.11.1/deploy/static/provider/aws/deploy.yaml
```


Clique [aqui](https://kubernetes.github.io/ingress-nginx/deploy/#aws) para acessar à documentação sobre a instalação do ingress controller `NETWORK LOAD BALANCER (NLB)` na AWS.

Para consultar se foi instalado corretamente.

```bash
kubectl get pods -n ingress-nginx
```

Vamos consultar o EXTERNAL-IP do nosso Ingress NGINX Controller.

```bash
kubectl get service -n ingress-nginx
```

Para criar um Ingress, precisamos ter um serviço rodando no cluster. Para isso, vamos criar um deployment e um serviço do Giropops Senha + Redis com os seguintes comandos (execute os comandos no diretório `pick/docs/k8s/day-11/files` pois os arquivos de configuração estão nesse diretório):

```bash
kubectl apply -f app-deployment.yaml
kubectl apply -f app-service.yaml
kubectl apply -f redis-deployment.yaml
kubectl apply -f redis-service.yaml
```

Vamos consultar nossos services.

```bash
kubectl get svc
```

### Criando um Ingress no EKS

Para criar um Ingress no EKS, precisamos criar um arquivo de configuração do Ingress `ingress-6.yaml` com o seguinte conteúdo:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: giropops-senhas
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: {DOMAIN_NAME}
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: giropops-senhas
            port:
              number: 5000
```

Para que o Ingress funcione corretamente, precisamos substituir o valor `{DOMAIN_NAME}` por um domínio válido. Inserindo uma entrada do tipo 'CNAME' no seu provedor de DNS, apontando para o endereço do Load Balancer do Ingress.


