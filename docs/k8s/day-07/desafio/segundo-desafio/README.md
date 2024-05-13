### Kubernetes Services

🔬 Neste desafio, você terá que resolver um problema com o nosso Deployment e Service do Giropops-Senhas. Você tem que descobrir o que está errado e corrigir o problema em 05 minutos.

Instruções
🕵️‍♀️ Verifique se o Deployment e o Service foram criados corretamente e se estão funcionando como esperado, senão, corrija o problema.
🚀 Use as guias "Terminal" e "Editor" para concluir este desafio. Após concluir as instruções, pressione o botão Check para verificar se você solucionou o desafio corretamente.

Solução:

giropops-senhas-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: giropops-senhas
  name: giropops-senhas
spec:
  replicas: 2
  selector:
    matchLabels:
      app: giropops-senhas
  template:
    metadata:
      labels:
        app: giropops-senhas
    spec:
      containers:
      - image: linuxtips/giropops-senhas:1.0
        name: giropops-senhas
        ports:
        - containerPort: 5000
        imagePullPolicy: Always        
```

giropops-senhas-service.yaml 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: giropops-senhas
  labels:
    app: giropops-senhas
spec:
  selector:
    app: giropops-senhas
  ports:
    - protocol: TCP
      port: 5000
      nodePort: 32500
      targetPort: 5000
      name: tcp-app
  type: NodePort

apiVersion: v1
kind: Service
metadata:
  name: giropops-senhas
  labels:
    app: giropops-senhas
spec:
  selector:
    app: giropops-senhas # Aqui a chave 'app' foi corrigida para 'giropops-senhas'
  ports:
    - protocol: TCP
      port: 5000
      nodePort: 32500
      targetPort: 5000 # O valor de targetPort foi alterado de 32767 para 5000
      name: tcp-app
  type: NodePort
```

nginx-headless-svc.yaml  

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

redis-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      name: redis
      labels:
        app: redis
    spec:
      containers:
      - image: redis
        name: redis
        ports:
          - containerPort: 6379
```

redis-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis-service
spec:
  selector:
    app: redis # selector app foi alterado de 'redis-service para 'redis'
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379 # O valor foi alterado de 6679 para 6379
  type: ClusterIP
```

statefulset-nginx.yaml

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: giropops-set
spec:
  serviceName: "nginx-sv"
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
        image: nginx:1.19.0  # A versão da imagem do nginx foi especificada como 'nginx:1.19.0'
        ports:
        - containerPort: 80
          name: http
        volumeMounts:
        - name: nginx-persistent-storage
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: nginx-persistent-storage
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
          requests:
            storage: 1Gi
```
comandos:

```bash
kubectl apply -f manifest/
```

ou

```bash
cd /manifest

kubectl apply -f giropops-senhas-deployment.yaml

kubectl apply -f giropops-senhas-service.yaml

kubectl apply -f nginx-headless-svc.yaml  

kubectl apply -f redis-deployment.yaml

kubectl apply -f redis-service.yaml

kubectl apply -f statefulset-ngnix.yaml
```