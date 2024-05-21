# PICK - Programa Intensivo em Containers e Kubernetes 2024 :computer:

## Day-9 Kubernetes :rocket:

##  Prometheus + Kubernetes


### O que é Prometheus?

Prometheus também é um projeto de código aberto mantido pela Cloud Native Computing Foundation (CNCF), que oferece um sistema de monitoramento e alerta. Ele foi originalmente desenvolvido pelo SoundCloud e é amplamente utilizado para monitorar sistemas e serviços em ambientes de computação em nuvem. Prometheus coleta métricas de vários alvos configurados através de um modelo de serviço de coleta de dados. Ele permite consultas flexíveis para análise de dados em tempo real, alertas baseados em regras definidas pelo usuário e geração de gráficos e visualizações para análise de tendências e desempenho. Em resumo, Prometheus é uma ferramenta valiosa para monitoramento e análise de sistemas distribuídos e ambientes de computação em nuvem.

### Prometheus Operator

O Prometheus Operator é um projeto de código aberto que estende as capacidades do Prometheus para gerenciamento e operação em ambientes de Kubernetes. Ele simplifica a configuração, implantação e gerenciamento do Prometheus e dos recursos relacionados, como alertas, regras de gravação e serviços de descoberta.

Basicamente, o Prometheus Operator automatiza tarefas como:

1. Implantação do Prometheus: Criação de instâncias do Prometheus em um cluster Kubernetes de forma automática e escalável.

2. Gerenciamento de configuração: Permite definir e gerenciar configurações complexas do Prometheus usando recursos declarativos do Kubernetes, como ConfigMaps e Custom Resource Definitions (CRDs).

3. Geração de regras de alerta: Facilita a definição de regras de alerta para monitorar métricas específicas e notificar os operadores sobre problemas potenciais.

4. Autodiscovery de serviços: Oferece suporte à descoberta automática de serviços para monitoramento pelo Prometheus.

Em resumo, o Prometheus Operator simplifica muito o processo de implantação e gerenciamento do Prometheus em ambientes Kubernetes, permitindo que os desenvolvedores e operadores se concentrem mais nas métricas e no monitoramento de seus aplicativos do que na configuração e manutenção do Prometheus em si.

### Kube-Prometheus

Kube-Prometheus é um projeto de código aberto que visa simplificar a implantação e o gerenciamento do Prometheus e de outras ferramentas relacionadas ao monitoramento em ambientes Kubernetes. Ele fornece um conjunto pré-configurado de ferramentas para monitorar aplicativos e infraestrutura em um cluster Kubernetes.

Esses são os componentes principais incluídos no pacote do Kube-Prometheus:

1. The Prometheus Operator: Este componente automatiza o provisionamento e a gestão de instâncias do Prometheus e dos recursos relacionados no Kubernetes.

2. Highly available Prometheus: O Prometheus é um sistema de monitoramento e alerta de código aberto que coleta métricas de vários alvos configurados. A configuração fornecida pelo Kube-Prometheus permite que o Prometheus seja altamente disponível, garantindo uma coleta de métricas robusta.

3. Highly available Alertmanager: O Alertmanager é uma ferramenta que gerencia alertas gerados pelo Prometheus. A configuração fornecida pelo Kube-Prometheus permite que o Alertmanager seja altamente disponível, garantindo a confiabilidade na entrega de notificações de alerta.

4. Prometheus node-exporter: Este componente é responsável por coletar métricas do sistema e do hardware das máquinas onde está instalado, fornecendo informações como uso de CPU, memória, disco, entre outros.

5. Prometheus blackbox-exporter: O blackbox-exporter é usado para monitorar serviços externos por meio de verificações de status HTTP, TCP e DNS.

6. Prometheus Adapter for Kubernetes Metrics APIs: Este adaptador permite que o Prometheus obtenha métricas do Kubernetes API Server, tornando-as acessíveis para monitoramento e alerta.

7. kube-state-metrics: O kube-state-metrics é um componente que expõe métricas sobre o estado dos recursos do Kubernetes, como pods, serviços e deployments. Isso permite que você monitore o estado e o desempenho do seu cluster Kubernetes.

8. Grafana: Grafana é uma plataforma de visualização de métricas e análises. Com o Grafana incluído no pacote, você pode criar dashboards personalizados para visualizar e analisar as métricas coletadas pelo Prometheus e outros componentes.

O Kube-Prometheus oferece scripts de instalação e configuração para facilitar a implantação desses componentes em um cluster Kubernetes. Ele também fornece modelos e dashboards pré-configurados para facilitar a visualização e análise das métricas coletadas. Em resumo, o Kube-Prometheus é uma solução abrangente para monitorar aplicativos e infraestrutura em ambientes Kubernetes, simplificando significativamente o processo de configuração e gerenciamento do monitoramento.

https://github.com/prometheus-operator/kube-prometheus


### Instalando o Kube-Prometheus

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

Aguarde até que os CRDs estejam prontos antes de continuar. Você pode verificar isso com o comando:

```bash
kubectl get crds | grep 'alertmanagers.monitoring.coreos.com\|podmonitors.monitoring.coreos.com\|probes.monitoring.coreos.com\|prometheuses.monitoring.coreos.com\|prometheusrules.monitoring.coreos.com\|servicemonitors.monitoring.coreos.com\|thanosrulers.monitoring.coreos.com'
```

Aplique os manifestos:

```bash
kubectl create -f manifests/
```

Após a aplicação dos manifests, você pode verificar se os componentes foram implantados corretamente:

```bash
kubectl get pods -n monitoring
```

Você deve ver pods para Prometheus, Alertmanager, Grafana, entre outros.

### Acessando o Grafana

```bash
kubectl --namespace monitoring port-forward svc/grafana 3000:3000
```

Vai estar disponível em: http://localhost:3000

### Acessando o Prometheus

```bash
kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090:9090
```

Vai estar disponível em: http://localhost:9090

### Acessando o Alertmanager

```bash
kubectl --namespace monitoring port-forward svc/alertmanager-main 9093:9093
```

Vai estar disponível em: http://localhost:9093

### Desinstalando o Kube-Prometheus

```bash
kubectl delete --ignore-not-found=true -f manifests/ -f manifests/setup
```

### Corrigindo o erro "too many open files" qunado estiver executando com Kind

Edite o arquivo '/etc/sysctl.conf':

```bash
sudo vim /etc/sysctl.conf
```

Adicione as seguintes linhas:

```bash
fs.inotify.max_user_watches = 524288
fs.inotify.max_user_instances = 512
```
