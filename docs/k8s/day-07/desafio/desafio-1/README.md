### Desafio StatefulSets e Headless Services no Kubernetes

🔬 Neste desafio, você vai configurar e manipular um StatefulSet e um Headless Service no Kubernetes. Seu objetivo é demonstrar sua compreensão desses conceitos cruciais do Kubernetes.

Instruções
IMPORTANTE: Tudo deve ser criado utilizando manifestos YAML. Os manifestos devem ser criados no diretório /root/manifests.

🏗️ Crie um StatefulSet do Nginx com três réplicas, cada uma com seu próprio volume persistente. O StatefulSet deve ser nomeado "giropops-set".

🎯 Configure um Headless Service para o StatefulSet criado. O serviço deve ser nomeado "giropops-service".

🕵️‍♀️ Verifique se o StatefulSet, o Service e o Pod foram criados corretamente e se estão em execução.

🔄 Faça um upgrade do StatefulSet para a versão 1.19.0 do Nginx.

🚀 Use as guias "Terminal" e "Editor" para concluir este desafio. Após concluir as instruções, pressione o botão Check para verificar se você solucionou o desafio corretamente.

&nbsp;


### Solução:


```bash
cd manifests
```

&nbsp;

Vamos criar o arquivo `statefulset-nginx.yaml` com o seguinte conteúdo:


```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: giropops-set
spec:
  serviceName: "nginx-svc"
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
          name: http
        volumeMounts:
        - name: nginx-data
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: nginx-data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
          requests:
            storage: 1Gi
```

&nbsp;


Vamos criar o arquivo `nginx-headless-svc.yaml` com o seguinte conteúdo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: giropops-service
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: http
  clusterIP: None
  selector:
    app: nginx
```

&nbsp;

Criando o `StatefulSet` com o comando:

```bash
kubectl apply -f statefulset-nginx.yaml 
```

Para verificar se o StatefulSet foi criado:

```bash
kubectl get statefulset
```

Criando o `Headless Services` com o comando:

```bash
kubectl apply -f nginx-headless-svc.yaml
```

Para verificar se o Headless Service foi criado:

```bash
kubectl get service
```


Vamos utilizar o comando `set image` que é uma subcomando do `kubectl` que permite atualizar as imagens dos contêineres em um recurso específico no cluster. Documentação <https://kubernetes.io/docs/reference/kubectl/generated/kubectl_set/kubectl_set_image/>

```yaml
kubectl set image statefulset/giropops-set nginx=nginx:1.19.0
```