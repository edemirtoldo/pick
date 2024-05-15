### Parte 2 

### Desafio Troubleshooting Kubernetes ConfigMaps e Secrets

🔬 Neste desafio, você terá que resolver um problema com o nosso ConfigMap e Secret do nosso Nginx. Você tem que descobrir o que está errado e corrigir o problema em 05 minutos.

Instruções
Alguma coisa aconteceu e o Nginx que você configurou no desafio anterior não está funcionando corretamente. Você precisa descobrir o que está errado e corrigir o problema.

🕵️‍♀️ Verifique se está tudo certo com o Pod, Secret, ConfigMap e outras coisas que você achar necessário.
🚀 Use as guias "Terminal" e "Editor" para concluir este desafio. Após concluir as instruções, pressione o botão Check para verificar se você solucionou o desafio corretamente.

Resposta:

Encontramos um problema no Secret, vamos remover o secret:

```bash
kubectl delete secret nginx-secret
```

Vamos criar um novo secret:

```bash
kubectl create secret tls nginx-secret --cert=nginx.crt --key=nginx.key
```
Vamos dar apply no pod:

```bash
kubectl apply -f pod-nginx-https.yaml
```