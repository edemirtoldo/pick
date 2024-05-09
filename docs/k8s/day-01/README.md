# Day-1 - Entendendo o que são Containers e Kubernetes



## O que é um container?

Um container é uma unidade padronizada de software que empacota o código e suas dependências, permitindo que um aplicativo seja executado de maneira consistente e eficiente em diferentes ambientes. Ele oferece isolamento, portabilidade e eficiência, sendo construído a partir de imagens que contêm tudo o que é necessário para o aplicativo funcionar.

## O que é um container engine?

Um container engine, é um software responsável por executar, criar e gerenciar contêineres. Ele fornece uma plataforma para criar e executar contêineres, lidando com tarefas como isolamento, networking e gerenciamento de recursos. O container engine interage com o sistema operacional do host para criar ambientes isolados (os contêineres) nos quais os aplicativos podem ser executados de forma independente. Além do Docker Engine, existem outros container engines populares, como containerd, rkt (Rocket), e CRI-O, cada um com suas próprias características e funcionalidades.


## O que é um container runtime?

Um container runtime é uma parte crucial do sistema que permite que os contêineres sejam executados em um ambiente de computação. Ele é responsável por executar os contêineres de maneira eficiente e segura, gerenciando aspectos como isolamento de recursos, redes e sistemas de arquivos. O container runtime é a camada que interpreta e executa as instruções contidas nas imagens dos contêineres

O containerd é um dos componentes fundamentais do ecossistema de contêineres Docker. Ele é um container runtime projetado para gerenciar o ciclo de vida dos contêineres, incluindo a execução, ação de parar, a exclusão e a supervisão de seus estados. Originalmente desenvolvido pela Docker, Inc., o containerd foi posteriormente doado para a Cloud Native Computing Foundation (CNCF), onde é mantido como um projeto de código aberto.

O containerd é modular e possui uma arquitetura flexível, o que o torna adequado para ser integrado em várias plataformas e sistemas. Ele opera como um daemon de alto desempenho que se comunica com outras partes do sistema, como o Docker Engine, Kubernetes ou outras ferramentas de orquestração de contêineres, para realizar suas operações.

Em resumo, o containerd é uma parte essencial do ecossistema de contêineres que fornece uma base sólida para a execução de contêineres, oferecendo funcionalidades robustas de gerenciamento de ciclo de vida e interoperabilidade com várias ferramentas e plataformas.

Os três tipos principais de container runtime são:

Docker Runtime (runc): Este é o runtime padrão utilizado pelo Docker. Ele é responsável por criar e executar os contêineres Docker, lidando com o isolamento de processos, controle de recursos e gerenciamento de sistemas de arquivos.

containerd: Este é um runtime de contêiner de nível inferior, que fornece funcionalidades de baixo nível para criar, iniciar, parar e supervisionar contêineres. O containerd é amplamente utilizado como o runtime subjacente em plataformas de contêineres, como o Docker e o Kubernetes.

CRI-O: Criado para ser uma implementação leve e otimizada do Kubernetes Container Runtime Interface (CRI), o CRI-O é projetado especificamente para integrar contêineres com o Kubernetes. Ele oferece uma alternativa ao Docker como runtime de contêiner para ambientes Kubernetes, fornecendo funcionalidades de execução de contêineres compatíveis com as especificações do Kubernetes.

## O que é OCI?

OCI significa "Open Container Initiative", que é uma organização sem fins lucrativos focada na padronização das tecnologias de contêineres. A missão da OCI é criar especificações abertas e comuns para formatos de imagem de contêiner, runtime de contêiner e outras tecnologias relacionadas a contêineres. Isso ajuda a garantir a interoperabilidade entre diferentes ferramentas e plataformas de contêineres, permitindo que os desenvolvedores construam e implantem aplicativos de maneira consistente em uma variedade de ambientes. As especificações da OCI são amplamente adotadas pela comunidade de contêineres e servem como base para muitas implementações populares, incluindo o Docker, o containerd e o CRI-O.

## O que é o Kubernetes?

O Kubernetes é uma plataforma de código aberto para automatizar a implantação, o dimensionamento e a operação de aplicativos em contêineres. Ele foi desenvolvido pelo Google e posteriormente doado à Cloud Native Computing Foundation (CNCF). O Kubernetes oferece uma infraestrutura robusta para gerenciar e orquestrar contêineres em ambientes de produção.

As principais características do Kubernetes incluem:

Orquestração de contêineres: O Kubernetes automatiza tarefas como implantação, escalonamento e gerenciamento de contêineres em clusters de hosts.

Escalabilidade: Ele permite escalar aplicativos de maneira rápida e eficiente, ajustando automaticamente o número de contêineres conforme necessário com base na demanda.

Automação: O Kubernetes suporta automação de operações comuns, como balanceamento de carga, recuperação de falhas e distribuição de tráfego.

Gerenciamento de recursos: Ele fornece recursos avançados para gerenciar recursos de computação, como CPU e memória, garantindo uma utilização eficiente dos recursos do cluster.

Portabilidade: O Kubernetes é uma plataforma agnóstica em relação ao provedor de nuvem, o que significa que pode ser executado em qualquer infraestrutura de nuvem pública ou privada.

Em resumo, o Kubernetes é um orquestrador de containers que os gerencia em escala, tornando mais fácil para os desenvolvedores e operadores implantar e manter aplicativos em ambientes de nuvem modernos.

## O que são os workers e o control plane do kubernetes ?

No Kubernetes, há uma distinção entre o control plane (plano de controle) e os workers (trabalhadores):

**Control Plane (Plano de Controle):** O control plane é o conjunto de componentes responsáveis por gerenciar e controlar o cluster Kubernetes como um todo. Ele coordena e supervisiona todas as operações do cluster, como a programação de contêineres, o monitoramento de recursos, a comunicação entre os nós e a manutenção do estado desejado do sistema. 
**Workers (Trabalhadores):** Os workers são os nós de computação onde os contêineres são executados. Eles são responsáveis por receber, programar e executar os contêineres conforme especificado pelo control plane. Os workers executam dois principais componentes:

Em resumo, o control plane gerencia e controla o cluster Kubernetes, enquanto os workers são responsáveis por executar os contêineres e lidar com as cargas de trabalho reais. Ambos os componentes são essenciais para o funcionamento correto e eficiente de um cluster Kubernetes.

## Quais os componentes do Control plane do Kubernetes?

Os principais componentes do control plane incluem:

- **kube-apiserver:** É a interface de comunicação para o cluster Kubernetes. Todas as interações entre os usuários e o cluster são feitas através do kube-apiserver.
- **etcd:** Um banco de dados distribuído consistente, usado para armazenar o estado do cluster Kubernetes. Ele mantém informações como configurações do cluster, metadados de objetos e estados do sistema.
- **kube-controller-manager:** É responsável por manter o estado desejado do cluster. Ele executa controladores que observam o estado dos recursos do Kubernetes e fazem ajustes para garantir que o estado atual corresponda ao estado desejado.
- **kube-scheduler:** É responsável por decidir em qual nó do cluster cada pod (unidade de trabalho no Kubernetes) deve ser executado, com base em requisitos de recursos, políticas de alocação e restrições definidas pelo usuário.s principais componentes do control plane incluem:

## Quais os componentes dos workres do Kubernetes?

Os workers executam dois principais componentes:

- **kubelet:** É um agente que roda em cada nó do cluster e é responsável por garantir que os contêineres estejam em execução no nó. Ele recebe as especificações de pod do control plane através do kube-apiserver e garante que os contêineres correspondentes estejam em execução.
- **kube-proxy:** É responsável por gerenciar o tráfego de rede no nó. Ele mantém as regras de encaminhamento de rede para permitir a comunicação entre os diferentes pods e serviços no cluster.

Em resumo, o control plane gerencia e controla o cluster Kubernetes, enquanto os workers são responsáveis por executar os contêineres e lidar com as cargas de trabalho reais. Ambos os componentes são essenciais para o funcionamento correto e eficiente de um cluster Kubernetes.

## Quais as portas TCP e UDP dos componentes do Kubernetes?

- **kube-apiserver:** TCP: 6443 
- **etcd:** TCP: 2379 - 2380
- **kube-scheduler:** TCP: 10251
- **kubelet:** TCP: 10250
- **kube-controller:** TCP: 10252

- **NodePort:** TCP: 30000 - 32767
- **WeaveNet:** TCP/UDP: 6783-6784

## Introdução a pods, replica sets, deployment e services?

## **pod**

Um pod é a menor unidade no Kubernetes que pode ser criada, agendada e gerenciada. Ele é a unidade básica de implantação e escalabilidade no Kubernetes. Um pod pode conter um ou mais contêineres, que são executados juntos em um ambiente compartilhado, com acesso à mesma rede e ao mesmo espaço de armazenamento.

Os pods são concebidos para serem efêmeros, o que significa que podem ser criados, destruídos e replicados dinamicamente conforme necessário. Eles fornecem uma abstração de nível mais alto para os contêineres, facilitando a orquestração e o gerenciamento de aplicativos distribuídos.

Os pods podem ser usados para agrupar contêineres que trabalham juntos, como uma aplicação de servidor da web com um contêiner para o aplicativo e outro para o banco de dados. Eles também podem ser usados para fornecer funcionalidades compartilhadas, como armazenamento em volume, configurações de rede e políticas de reinicialização.

Em resumo, um pod no Kubernetes é uma unidade lógica que encapsula um ou mais contêineres e fornece um ambiente compartilhado para sua execução. Ele é fundamental para a implantação e o gerenciamento de aplicativos distribuídos em um ambiente de contêineres.

## **replicaset**

Um ReplicaSet é um recurso no Kubernetes que define e mantém um conjunto de réplicas de pods idênticos. Ele garante que um número especificado de réplicas de um pod esteja sempre em execução no cluster. Se houver menos réplicas do que o especificado, o ReplicaSet criará novos pods para restaurar o número desejado. Se houver mais pods do que o desejado, o ReplicaSet os escalará para baixo, garantindo que o número correto de réplicas esteja sempre em execução.

O ReplicaSet é usado principalmente para garantir a disponibilidade e a confiabilidade dos aplicativos, permitindo a escala horizontal dos pods. Por exemplo, você pode usar um ReplicaSet para garantir que três réplicas de um aplicativo da web estejam sempre em execução, distribuídas entre diferentes nós do cluster. Se um pod falhar ou for removido por qualquer motivo, o ReplicaSet criará automaticamente um novo pod para substituí-lo, mantendo o número total de réplicas.

O ReplicaSet é frequentemente gerenciado por controladores de nível superior, como o Deployment, que fornece funcionalidades adicionais, como atualizações de versão e rollback de aplicativos. No entanto, os ReplicaSets também podem ser usados independentemente quando a escalabilidade e a alta disponibilidade dos pods são os principais requisitos.

## **deployment**

Um Deployment é um recurso no Kubernetes que gerencia a implantação de aplicativos em contêineres de maneira declarativa e controlada. Ele fornece uma maneira conveniente de definir, atualizar e dimensionar aplicativos distribuídos em um cluster Kubernetes.

Principais características de um Deployment:

**Declarativo:** O Deployment permite definir o estado desejado do aplicativo em um arquivo de manifesto YAML. Isso inclui informações sobre a imagem do contêiner, número de réplicas, estratégia de atualização e configurações de rollout.

**Atualização controlada:** O Deployment gerencia o processo de atualização de aplicativos de forma controlada. Ele pode atualizar os pods gradualmente (rollout) ou simultaneamente (recreate), permitindo que você monitore e reverta atualizações de maneira segura.

**Rollback automático:** Se uma atualização de aplicativo causar problemas ou falhar em atender aos critérios de saúde definidos, o Deployment pode reverter automaticamente para a versão anterior, garantindo a continuidade do serviço.

**Escalonamento automático:** O Deployment pode ajustar dinamicamente o número de réplicas do aplicativo com base na demanda de recursos. Isso permite que o aplicativo se adapte a picos de tráfego ou a variações na carga de trabalho.

**Gerenciamento de configuração:** O Deployment suporta a definição e o gerenciamento de configurações de ambiente, variáveis de ambiente e segredos, permitindo uma configuração flexível e segura dos aplicativos.

Em resumo, um Deployment no Kubernetes é uma maneira poderosa de gerenciar a implantação e a atualização de aplicativos de forma segura e automatizada em um ambiente de contêineres distribuídos. Ele simplifica o processo de implantação e oferece recursos avançados para garantir a confiabilidade e a disponibilidade dos aplicativos em produção.

## **service**

Um Service é um recurso no Kubernetes que define um conjunto de pods e uma política de acesso a esses pods. Ele fornece uma maneira abstrata de expor aplicativos executados em um conjunto de pods como um serviço de rede, permitindo que outros aplicativos no cluster se comuniquem com eles de forma confiável.

Principais características de um Service:

**Descoberta de serviço:** Um Service permite que os aplicativos se comuniquem com outros aplicativos por meio de um nome DNS estável, em vez de se preocupar com endereços IP dinâmicos dos pods. Isso facilita a descoberta e a comunicação entre os componentes do aplicativo.

**Balanceamento de carga:** Um Service distribui automaticamente o tráfego de entrada entre os pods do aplicativo, garantindo uma distribuição uniforme da carga e melhorando a disponibilidade e a escalabilidade do aplicativo.

**Endereço IP estável:** Um Service é associado a um endereço IP virtual (ClusterIP) dentro do cluster Kubernetes, que permanece estável e acessível independentemente de quais pods estejam em execução ou onde estejam implantados.

**Portas nomeadas:** Um Service pode expor um ou mais ports nomeados, permitindo que os aplicativos se comuniquem por meio de portas conhecidas e significativas, em vez de números de porta arbitrários.

**Suporte a múltiplos protocolos:** Um Service suporta a exposição de serviços através de TCP, UDP ou ambos, permitindo uma variedade de protocolos de comunicação.

Em resumo, um Service no Kubernetes é uma abstração que permite expor aplicativos executados em pods como serviços de rede, facilitando a comunicação entre os componentes do aplicativo e garantindo a disponibilidade e a escalabilidade do mesmo.

## Entendendo e instalando o kubectl.

O **kubectl** é uma ferramenta de linha de comando usada para interagir com clusters Kubernetes. Ele permite aos usuários executar várias operações administrativas e de gerenciamento em um cluster Kubernetes, como criar, visualizar, atualizar e excluir recursos do Kubernetes.

Principais características do kubectl:

1. **Gerenciamento de recursos:** O kubectl permite criar, listar, atualizar e excluir uma ampla variedade de recursos do Kubernetes, como pods, deployments, services, configmaps, secrets, e muito mais.

2. **Acesso ao cluster:** Ele facilita o acesso a um cluster Kubernetes, permitindo que os usuários executem comandos diretamente contra o cluster a partir de suas máquinas locais ou de qualquer outra máquina que tenha conectividade com o cluster.

3. **Configuração:** O kubectl é configurado para se conectar a um cluster Kubernetes específico por meio de um arquivo de configuração (geralmente localizado em ~/.kube/config) que contém informações de autenticação, contexto e cluster.

4. **Interação com a API:** Por meio do kubectl, os usuários podem interagir diretamente com a API do Kubernetes, permitindo o acesso a recursos de baixo nível e a execução de operações avançadas no cluster.

5. **Plugins:** O kubectl suporta plugins, o que permite estender suas funcionalidades com comandos personalizados e adicionais.

Em resumo, o kubectl é uma ferramenta essencial para administradores, desenvolvedores e operadores que desejam gerenciar clusters Kubernetes de forma eficiente e produtiva por meio da linha de comando. Ele fornece uma interface poderosa e flexível para interagir com clusters Kubernetes e executar uma variedade de tarefas de gerenciamento de recursos.

Clique [aqui](https://kubernetes.io/pt-br/docs/tasks/tools/) para documentação de como instalar o **kubectl**.

## Criando nosso primeiro cluster com o kind.

**kind**

O Kind (Kubernetes in Docker) é uma ferramenta de linha de comando que permite criar clusters Kubernetes usando contêineres Docker como nós do cluster. Ele simplifica o processo de criação de clusters Kubernetes locais e de teste, fornecendo uma maneira fácil e rápida de configurar um ambiente Kubernetes em sua máquina local.

Principais características do Kind:

1. **Simplicidade:** O Kind simplifica o processo de criação de clusters Kubernetes locais, eliminando a necessidade de configurações complexas ou provisionamento de máquinas virtuais separadas.

2. **Rapidez:** Com o Kind, é possível criar e iniciar um cluster Kubernetes em apenas alguns segundos, permitindo que os desenvolvedores testem rapidamente aplicativos em um ambiente Kubernetes local.

3. **Usa contêineres Docker:** O Kind usa contêineres Docker para criar os nós do cluster Kubernetes, o que significa que não é necessário instalar ou configurar outras ferramentas de virtualização.

4. **Configuração flexível:** O Kind oferece opções de configuração flexíveis, permitindo especificar o número de nós, as versões do Kubernetes e outras configurações do cluster conforme necessário.

5. **Integração com ferramentas de desenvolvimento:** Como o Kind cria clusters Kubernetes locais, ele é facilmente integrado com ferramentas de desenvolvimento e fluxos de trabalho de CI/CD, permitindo testes e desenvolvimento mais eficientes.

Em resumo, o Kind é uma ferramenta útil para desenvolvedores que desejam criar clusters Kubernetes locais rapidamente e sem complicações, permitindo que eles testem e desenvolvam aplicativos em um ambiente Kubernetes sem a necessidade de provisionar recursos adicionais.


Clique [aqui](https://kind.sigs.k8s.io/docs/user/quick-start/#installation) para instalar o **kind**.

## Criando cluster com o kind

## Primeiros passos no Kubernetes no kubectl




~~~bash
kubectl get nodes
~~~

~~~bash
kubectl get 
~~~





