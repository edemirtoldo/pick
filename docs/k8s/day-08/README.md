# PICK - Programa Intensivo em Containers e Kubernetes 2024 :computer:

## Day-7 Kubernetes :rocket:

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


