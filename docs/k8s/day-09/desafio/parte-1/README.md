### Desafio parte 1

### Descomplicando o Kubernetes e o Prometheus - Day-8

### 🚢 Deploy Kube-Prometheus


🎯 É hora de colocar o Prometheus para jogar no campo do Kubernetes!

Resposta:

Clone o repositório do Kube-Prometheus:

```bash
git clone https://github.com/prometheus-operator/kube-prometheus.git
```

Acesse o diretório do Kube-Prometheus:

```bash
cd kube-prometheus
```

Crie o namespace e o CRDs:

```bash
kubectl create -f manifests/setup
```

Aplique os manifestos:

```bash
kubectl create -f manifests/
```