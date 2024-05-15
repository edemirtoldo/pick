Desafio Nginx com HTTPS no Kubernetes
🔬 Neste desafio, você precisa configurar o Nginx com HTTPS em um cluster Kubernetes. Isso envolve a criação e manipulação de Secrets e ConfigMaps. Seu objetivo é demonstrar sua compreensão dos conceitos e habilidades práticas em Kubernetes.

Instruções
IMPORTANTE: Todos os arquivos devem ser criados no diretório /root/manifests, inclusivo o arquivo nginx.conf e o certificado e a chave privada.

🏗️ Crie um Secret no Kubernetes que contenha o certificado e a chave privada para o HTTPS. Verifique se o Secret foi criado corretamente e se o tipo está correto.

🎯 Crie um ConfigMap com a configuração do Nginx. Certifique-se de que a configuração está correta e de que o ConfigMap está corretamente configurado.

💼 Verifique se o Nginx está rodando e se seu status é "Running".

🕵️‍♀️ Certifique-se de que o HTTPS está funcionando corretamente. Para fazer isso, você pode executar curl -k https://localhost:<PORTA>.

📝 Verifique o certificado do Nginx e certifique-se de que está correto.

💡 Verifique se o Service é do tipo NodePort na porta 32400 e está redirecionando para a porta 443 do container.

📝 Verifique se o Nginx está rodando na porta 443.

🎯 O nome do Pod precisa ser nginx-https.

🎯 O nome do Service precisa ser nginx-service.

🎯 O nome do ConfigMap precisa ser nginx-config.

🎯 O nome do Secret precisa ser nginx-secret.

🎯 O certificado e a chave privada precisam estar no diretório /root/manifests.

🎯 O arquivo de configuração do Nginx precisa estar no diretório /root/manifests.

🎯 O arquivo de configuração do Nginx precisa se chamar nginx.conf.

🎯 O certificado precisa se chamar nginx.crt.

🎯 A chave privada precisa se chamar nginx.key.

🎯 O manifesto do Pod precisa se chamar nginx-https-pod.yaml.

🚀 Use as guias "Terminal", "Editor" e "Navegador" para concluir este desafio. Após concluir as instruções, pressione o botão Check para verificar se você solucionou o desafio corretamente.

Desafio - parte 1

Criar um certificado TLS e uma chave privada:

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout nginx.key -out nginx.crt 
```

Criar o Secret:

```bash
kubectl create secret tls nginx-secret --cert=nginx.crt --key=nginx.key
```

Criar o ConfigMap nginx-config:

```bash
vim nginx.conf
```

```bash
kubectl create configmap nginx-config --from-file=nginx.conf
```

Criar o pod:

```bash
vim pod-nginx-https.yaml
```

```bash
kubectl apply -f pod-nginx-https.yaml
```

Criar o service:

```bash
vim nginx-service.yaml
```

```bash
kubectl apply -f nginx-service.yaml
```
XXXXXX


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
