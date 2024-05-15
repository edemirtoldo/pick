### Parte 2 

### Desafio Troubleshooting Kubernetes ConfigMaps e Secrets

🔬 Neste desafio, você terá que resolver um problema com o nosso ConfigMap e Secret do nosso Nginx. Você tem que descobrir o que está errado e corrigir o problema em 05 minutos.

Instruções
Alguma coisa aconteceu e o Nginx que você configurou no desafio anterior não está funcionando corretamente. Você precisa descobrir o que está errado e corrigir o problema.

🕵️‍♀️ Verifique se está tudo certo com o Pod, Secret, ConfigMap e outras coisas que você achar necessário.
🚀 Use as guias "Terminal" e "Editor" para concluir este desafio. Após concluir as instruções, pressione o botão Check para verificar se você solucionou o desafio corretamente.

Resposta:

```bash
kubectl describe pods nginx-https
```




```bash
kubectl delete -f pod-nginx-https.yaml
```

```bash
kubectl delete secret nginx-secret
```

```bash
kubectl create secret tls nginx-secret --cert=nginx.crt --key=nginx.key
```

```bash
kubectl apply -f pod-nginx-https.yaml
```