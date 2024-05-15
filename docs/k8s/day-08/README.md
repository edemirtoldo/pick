# PICK - Programa Intensivo em Containers e Kubernetes 2024 :computer:

## Day-8 Kubernetes :rocket:

## Secrets e ConfigMaps


### O que são Secrets?

Os Secrets fornecem uma maneira segura e flexível de gerenciar informações sensíveis, como senhas, tokens OAuth, chaves SSH e outros dados que você não quer expor nas configurações de seus aplicaçãos.

Um Secret é um objeto que contém um pequeno volume de informações sensíveis, como uma senha, um token ou uma chave. Essas informações podem ser consumidas por Pods ou usadas pelo sistema para realizar ações em nome de um Pod.

 

### Como os Secrets funcionam

Os Secrets são armazenados no Etcd, o armazenamento de dados distribuído do Kubernetes. Por padrão, eles são armazenados sem criptografia, embora o Etcd suporte criptografia para proteger os dados armazenados nele. Além disso, o acesso aos Secrets é restrito por meio de Role-Based Access Control (RBAC), o que permite controlar quais usuários e Pods podem acessar quais Secrets.

Os Secrets podem ser montados em Pods como arquivos em volumes ou podem ser usados para preencher variáveis de ambiente para um container dentro de um Pod. Quando um Secret é atualizado, o Kubernetes não atualiza automaticamente os volumes montados ou as variáveis de ambiente que se referem a ele.

 

### Tipos de Secrets

Existem vários tipos de Secrets que você pode usar, dependendo de suas necessidades específicas. Abaixo estão alguns dos tipos mais comuns de Secrets:

- Opaque Secrets - são os Secrets mais simples e mais comuns. Eles armazenam dados arbitrários, como chaves de API, senhas e tokens. Os Opaque Secrets são codificados em base64 quando são armazenados no Kubernetes, mas não são criptografados. Eles podem ser usados para armazenar dados confidenciais, mas não são seguros o suficiente para armazenar informações altamente sensíveis, como senhas de banco de dados.

- kubernetes.io/service-account-token - são usados para armazenar tokens de acesso de conta de serviço. Esses tokens são usados para autenticar Pods com o Kubernetes API. Eles são montados automaticamente em Pods que usam contas de serviço.

- kubernetes.io/dockercfg e kubernetes.io/dockerconfigjson - são usados para armazenar credenciais de registro do Docker. Eles são usados para autenticar Pods com um registro do Docker. Eles são montados em Pods que usam imagens de container privadas.

- kubernetes.io/tls, kubernetes.io/ssh-auth e kubernetes.io/basic-auth - são usados para armazenar certificados TLS, chaves SSH e credenciais de autenticação básica, respectivamente. Eles são usados para autenticar Pods com outros serviços.

- bootstrap.kubernetes.io/token - são usados para armazenar tokens de inicialização de cluster. Eles são usados para autenticar nós com o plano de controle do Kubernetes.

Tem mais alguns tipos de Secrets, mas esses são os mais comuns. Você pode encontrar uma lista completa de tipos de Secrets na documentação do Kubernetes.

Cada tipo de Secret tem um formato diferente. Por exemplo, os Secrets Opaque são armazenados como um mapa de strings, enquanto os Secrets TLS são armazenados como um mapa de strings com chaves adicionais para armazenar certificados e chaves, por isso é importante saber qual tipo de Secret você está usando para que você possa armazenar os dados corretamente.

&nbsp;

### Criando um secret - Opaque

Criando a senha `giropops`com o comando base64 para ser utilizado no secret.

```bash
echo -n "giropops" | base64
```
O comando acima irá retornar a string `Z2lyb3BvcHM=`.

Criando o nome do usuário `nuderval`com o comando base64 para ser utilizado no secret.

```bash
echo -n "nuderval" | base64
```
O comando acima irá retornar a string `bnVkZXJ2YWw=`.

Secret `primeiro-secret.yaml`.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: giropops-secret
type: Opaque
data:
  username: bnVkZXJ2YWw=
  password: Z2lyb3BvcHM=
```

Criando o Secret usando o comando kubectl apply, vá com o seguinte comando:

```bash
kubectl apply -f primeiro-secret.yaml 
```

Primeiro Secret foi criado! Agora você pode ver o Secret usando o comando kubectl get:

```bash
kubectl get secret

NAME              TYPE     DATA   AGE
giropops-secret   Opaque   2      74s
```

Para ver os dados armazenados no Secret, você pode usar o comando kubectl get com a opção -o yaml:

```bash
kubectl get secret giropops-secret -o yaml
```
Será apresentado o seguinte resultado em yaml:

```yaml
apiVersion: v1
data:
  password: Z2lyb3BvcHM=
  username: bnVkZXJ2YWw=
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"password":"Z2lyb3BvcHM=","username":"bnVkZXJ2YWw="},"kind":"Secret","metadata":{"annotations":{},"name":"giropops-secret","namespace":"default"},"type":"Opaque"}
  creationTimestamp: "2024-05-14T14:49:17Z"
  name: giropops-secret
  namespace: default
  resourceVersion: "186084"
  uid: 467b2b3e-ed14-4856-9fd2-823ab3c9bb26
type: Opaque
```
Você também pode ver os detalhes do Secret usando o comando `kubectl describe`:

```bash
kubectl describe secret giropops-secret
```
o resultado apresentado:

```bash
Name:         giropops-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  8 bytes
username:  8 bytes
```

Caso você queira criar esse mesmo Secret usando o comando `kubectl create secret`, você pode executar o seguinte comando:

Primeiro vamos excluir o secret anterior:

```bash
kubectl delete -f primeiro-secret.yaml 
```
Agora o comando `kubectl create secret`:

```bash
kubectl create secret generic giropops-test --from-literal=username=bnVkZXJ2YWw= --from-literal=password=Z2lyb3BvcHM=
```
Vamos verificar se foi criando o secret com o comando:

```bash
kubectl get secret
```

O resultado apresentado é:

```bash
NAME            TYPE     DATA   AGE
giropops-test   Opaque   2      3s
```

Para verificar os detalhes deste secrets criando, vamos utilizar o seguinte comando:

```bash
kubectl describe secrets giropops-test
```
O resultado é o seguinte:

```bash
Name:         giropops-test
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
username:  12 bytes
password:  12 bytes
```

### Colando o secret dentro de um pod

Criando o `pod-secret.yaml`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: giropops-pod
spec:
  containers:
    - name: giropops-container
      image: nginx
      env:
        - name: USERNAME
          valueFrom:
            secretKeyRef:
              name: giropops-test
              key: username
        - name: PASSWORD
          valueFrom:
            secretKeyRef:
              name: giropops-test
              key: password
```

Criando o Pod usando o comando `kubectl apply`:

```bash
kubectl apply -f pod-secret.yaml 
```

```bash
kubectl get pods
```
O Resultado:

```bash
NAME           READY   STATUS    RESTARTS   AGE
giropops-pod   1/1     Running   0          94s
```
Vamos ver os detahes deste pod:

```bash
kubectl describe pods giropops-pod 
```

```bash
Name:             giropops-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             giropops-worker/192.168.16.3
Start Time:       Tue, 14 May 2024 13:37:36 -0300
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.244.1.2
IPs:
  IP:  10.244.1.2
Containers:
  giropops-container:
    Container ID:   containerd://18fcaf7c8e33eb325ff9b6e6fcd5fd6b744172783fdedf6dbc22237bb071b591
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:32e76d4f34f80e479964a0fbd4c5b4f6967b5322c8d004e9cf0cb81c93510766
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Tue, 14 May 2024 13:37:41 -0300
    Ready:          True
    Restart Count:  0
    Environment:
      USERNAME:  <set to the key 'username' in secret 'giropops-test'>  Optional: false
      PASSWORD:  <set to the key 'password' in secret 'giropops-test'>  Optional: false
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-hjth5 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-hjth5:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  3m14s  default-scheduler  Successfully assigned default/giropops-pod to giropops-worker
  Normal  Pulling    3m13s  kubelet            Pulling image "nginx"
  Normal  Pulled     3m9s   kubelet            Successfully pulled image "nginx" in 3.701840064s (3.701851747s including waiting)
  Normal  Created    3m9s   kubelet            Created container giropops-container
  Normal  Started    3m9s   kubelet            Started container giropops-container
```

Vamos acessar o pod para verificar se as variaveis de ambiente estão no pod.

```bash
kubectl exec -ti giropops-pod -- bash
```
Dentro do pod vamos executar o seguinte comando:

```bash
set
```

será apresentado o seguinte conteudo:

```bash
BASH=/usr/bin/bash
BASHOPTS=checkwinsize:cmdhist:complete_fullquote:expand_aliases:extquote:force_fignore:globasciiranges:globskipdots:hostcomplete:interactive_comments:patsub_replacement:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=([0]="0")
BASH_ARGV=()
BASH_CMDS=()
BASH_LINENO=()
BASH_LOADABLES_PATH=/usr/local/lib/bash:/usr/lib/bash:/opt/local/lib/bash:/usr/pkg/lib/bash:/opt/pkg/lib/bash:.
BASH_SOURCE=()
BASH_VERSINFO=([0]="5" [1]="2" [2]="15" [3]="1" [4]="release" [5]="x86_64-pc-linux-gnu")
BASH_VERSION='5.2.15(1)-release'
COLUMNS=72
DIRSTACK=()
EUID=0
GROUPS=()
HISTFILE=/root/.bash_history
HISTFILESIZE=500
HISTSIZE=500
HOME=/root
HOSTNAME=giropops-pod
HOSTTYPE=x86_64
IFS=$' \t\n'
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
LINES=14
MACHTYPE=x86_64-pc-linux-gnu
MAILCHECK=60
NGINX_VERSION=1.25.5
NJS_RELEASE=3~bookworm
NJS_VERSION=0.8.4
OPTERR=1
OPTIND=1
OSTYPE=linux-gnu
PASSWORD=Z2lyb3BvcHM=
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PIPESTATUS=([0]="1")
PKG_RELEASE=1~bookworm
PPID=0
PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
PS2='> '
PS4='+ '
PWD=/
SHELL=/bin/bash
SHELLOPTS=braceexpand:emacs:hashall:histexpand:history:interactive-comments:monitor
SHLVL=1
TERM=xterm
UID=0
USERNAME=bnVkZXJ2YWw=
_=']'
```

Fora do pod podemos utilizar outro comando para ver as variaveis:

```bash
kubectl exec -ti giropops-pod -- env
```
O resultado será:

```bash

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=giropops-pod
NGINX_VERSION=1.25.5
NJS_VERSION=0.8.4
NJS_RELEASE=3~bookworm
PKG_RELEASE=1~bookworm
USERNAME=bnVkZXJ2YWw=
PASSWORD=Z2lyb3BvcHM=
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
TERM=xterm
HOME=/root
```

### Criando um Secret do tipo Docker Registry (dockercfg)
Faça o login no Docker Hub:

```bash
docker login
```

Pegue o conteúdo do arquivo ~/.docker/config.json em base64:

```bash
cat ~/.docker/config.json | base64
```

Crie o arquivo secret.yaml:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: dockerhub-secret
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: |
    {CONTEÚDO DO ARQUIVO ~/.docker/config.json EM BASE64}
```
exit
```bash
kubectl apply -f secret.yaml
```

#### Utilizando um Secret do tipo Docker Registry (dockercfg) como variável de ambiente

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: giropops-pod
spec:
  containers:
    - name: giropops-container
      image: {DOCKERHUB_USERNAME}/{IMAGE_NAME}
  imagePullSecrets:
  - name: dockerhub-secret
```

```bash
kubectl apply -f pod.yaml
```

### Criando um Secret TLS

O Secret kubernetes.io/tls, é usado para armazenar certificados TLS e chaves privadas. Eles são usados para fornecer segurança na comunicação entre os serviços no Kubernetes. Por exemplo, você pode usar um Secret TLS para configurar o HTTPS no seu serviço web.

Para criar um Secret TLS, você precisa ter um certificado TLS e uma chave privada, e você precisa codificar o certificado e a chave privada em base64, para então criar o Secret.

Vamos criar um Secret TLS para o nosso serviço web, mas para isso, você precisa ter um certificado TLS e uma chave privada antes de mais nada.

Para criar um certificado TLS e uma chave privada, você pode usar o comando openssl:

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout chave-privada.key -out certificado.crt
 ```
Com o certificado TLS e a chave privada criados, vamos criar o nosso Secret, é somente para mudar um pouco, vamos criar o Secret usando o comando kubectl apply:

```bash
kubectl create secret tls meu-servico-web-tls-secret --cert=certificado.crt --key=chave-privada.key
```
resultado:

```bash
secret/meu-servico-web-tls-secret created
```
Vamos conferir o secret criado

```bash
kubectl get secrets meu-servico-web-tls-secret
```
Resultado:

```bash
NAME                         TYPE                DATA   AGE
meu-servico-web-tls-secret   kubernetes.io/tls   2      108s
```
Ver em formaqto yaml

```bash
kubectl get secrets meu-servico-web-tls-secret -o yaml
```

Vamos criar o arquivo de configuração do Nginx chamado nginx.conf, que vai ser usado pelo ConfigMap:

```bash
events { } # configuração de eventos

http { # configuração do protocolo HTTP, que é o protocolo que o Nginx vai usar
  server { # configuração do servidor
    listen 80; # porta que o Nginx vai escutar
    listen 443 ssl; # porta que o Nginx vai escutar para HTTPS e passando o parâmetro ssl para habilitar o HTTPS
    
    ssl_certificate /etc/nginx/tls/certificado.crt; # caminho do certificado TLS
    ssl_certificate_key /etc/nginx/tls/chave-privada.key; # caminho da chave privada

    location / { # configuração da rota /
      return 200 'Bem-vindo ao Nginx!\n'; # retorna o código 200 e a mensagem Bem-vindo ao Nginx!
      add_header Content-Type text/plain; # adiciona o header Content-Type com o valor text/plain
    } 
  }
}
``` 

Agora vamos criar o ConfigMap nginx-config com o arquivo de configuração do Nginx:

```bash
kubectl create configmap nginx-config --from-file=nginx.conf
```
Vamos verificar a criação:

```bash
kubectl get configmaps 
```
Resultado:

```bash
NAME               DATA   AGE
kube-root-ca.crt   1      5d23h
nginx-config       1      13s
```

```bash
kubectl describe configmaps 
```

Criando um ConfigMap chamado nginx-config com o conteúdo do arquivo `configmap.yaml`. Podemos fazer a mesma coisa através de um manifesto, como no exemplo abaixo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
  namespace: default
data:
  nginx.conf: |
    events { }

    http {
      server {
        listen 80;
        listen 443 ssl;

        ssl_certificate /etc/nginx/tls/certificado.crt;
        ssl_certificate_key /etc/nginx/tls/chave-privada.key;

        location / {
          return 200 'Bem-vindo ao Nginx!\n';
          add_header Content-Type text/plain;
        }
      }
    }
```

deededed


Vamos fazer o apply

```bash
kubectl apply -f pod-configmap-secret.yaml 
```

```
kubectl get pods
```

``` bash
NAME           READY   STATUS    RESTARTS   AGE
giropops-pod   1/1     Running   0          7s
```

```bash
kubectl describe pods giropops-pod 
```
Resultado:

```bash
Name:             giropops-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             giropops-worker/192.168.16.4
Start Time:       Wed, 15 May 2024 12:23:26 -0300
Labels:           app=nginx
Annotations:      <none>
Status:           Running
IP:               10.244.1.3
IPs:
  IP:  10.244.1.3
Containers:
  giropops-container:
    Container ID:   containerd://d6c7f0fd7d5d9bfe33779e69f0fa628a13f5b631d61b31a6e8a7046d53aeb214
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:a484819eb60211f5299034ac80f6a681b06f89e65866ce91f356ed7c72af059c
    Ports:          80/TCP, 443/TCP
    Host Ports:     0/TCP, 0/TCP
    State:          Running
      Started:      Wed, 15 May 2024 12:23:28 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /etc/nginx/nginx.conf from nginx-config-volume (rw,path="nginx.conf")
      /etc/nginx/tls from nginx-tls (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-6xnqb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  nginx-config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      nginx-config
    Optional:  false
  nginx-tls:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  meu-servico-web-tls-secret
    Optional:    false
  kube-api-access-6xnqb:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  79m   default-scheduler  Successfully assigned default/giropops-pod to giropops-worker
  Normal  Pulling    79m   kubelet            Pulling image "nginx"
  Normal  Pulled     79m   kubelet            Successfully pulled image "nginx" in 1.454994264s (1.455011385s including waiting)
  Normal  Created    79m   kubelet            Created container giropops-container
  Normal  Started    79m   kubelet            Started container giropops-container
```

Vamos fazer o expose:

```bash
kubectl expose pods giropops-pod 
```

```bash
kubectl get service
```
Resultado:

```bash
NAME           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
giropops-pod   ClusterIP   10.96.23.150   <none>        80/TCP,443/TCP   17s
kubernetes     ClusterIP   10.96.0.1      <none>        443/TCP          6d2h
```

Fazer um port-forward para um teste de acesso:

```bash
kubectl port-forward services/giropops-pod 4443:443
```

Vamos acessar o link no navegador:

https://localhost:4443/

ou

```bash
curl -k https://localhost:4443

Bem-vindo ao Nginx!
```

