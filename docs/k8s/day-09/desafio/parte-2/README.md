### Desafio parte 2

### Descomplicando o Kubernetes e o Prometheus - Day-8

### 🖥️ Acessar o Grafana do nosso cluster

Quando você logar pela primeira vez com o usuário admin, mude a senha imediatamente.

Durante esse desafio, você terá que usar o 'kubectl port-forward' para deixar o grafana disponível na porta 33000

Um conselho importante, você vai precisar utilizar o parâmetro "--address=0.0.0.0" para que seja possível você acessar o Grafana através do navegador.

Explore os dashboards, e divirta-se!

Quando terminar, clique em Check!

Resposta:

```bash
kubectl port-forward -n monitoring svc/grafana 33000:3000 --address=0.0.0.0
```