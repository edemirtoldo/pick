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

```yaml
kubectl apply -f statefulset-nginx.yaml 
```

Criando o `Headless Services` com o comando:

```yaml
kubectl apply -f nginx-headless-svc.yaml
```


Vamos utilizar o comando `set image` que é uma subcomando do `kubectl` que permite atualizar as imagens dos contêineres em um recurso específico no cluster.

```yaml
kubectl set image statefulset/giropops-set nginx=nginx:1.19.0
```