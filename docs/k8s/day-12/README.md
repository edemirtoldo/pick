# PICK - Programa Intensivo em Containers e Kubernetes 2024 :computer:

## Day-12 Kubernetes :rocket:

## Cert-Manager, Annotations e Labels 

### [Cert-manager](https://cert-manager.io/)

Cert-manager é uma ferramenta de código aberto que ajuda a gerenciar certificados SSL/TLS em clusters Kubernetes. Ele automatiza a aquisição, renovação e gerenciamento de certificados de diversas fontes, como Let's Encrypt, HashiCorp Vault, Venafi e outras autoridades certificadoras (CAs).

#### Funcionalidades Principais

1. **Automatização de Certificados:** Cert-manager permite que você configure regras para solicitar e renovar certificados automaticamente, garantindo que os serviços em seu cluster Kubernetes estejam sempre usando certificados válidos.

2. **Integração com Várias CAs:** Suporta várias CAs, como Let's Encrypt, ACME (Automatic Certificate Management Environment) e outras, oferecendo flexibilidade na escolha do provedor de certificados.

3. **Suporte a Diversos Tipos de Certificados:** Além de certificados padrão, o cert-manager pode gerenciar certificados wildcard (curinga) e certificados autoassinados.

4. **Configuração via Kubernetes CRDs:** Utiliza Custom Resource Definitions (CRDs) do Kubernetes para gerenciar certificados e emissoras (issuers). As CRDs permitem definir como e quando os certificados devem ser solicitados e renovados.

5. **Renovação Automática:** Cert-manager monitora a validade dos certificados e os renova automaticamente antes que expirem, reduzindo o risco de interrupções devido a certificados expirados.

6. **Suporte a Integrações Complexas:** Pode ser configurado para trabalhar com Ingress controllers, permitindo a configuração automática de HTTPS para serviços expostos externamente.

O `Let's Encrypt` é uma autoridade certificadora (CA) gratuita e automatizada que oferece certificados SSL/TLS. Ele fornece dois ambientes principais para testar e produzir certificados: o ambiente de produção (production) e o ambiente de preparo (staging).

1. **Let's Encrypt Production (letsencrypt-production):**

- Este ambiente é usado para emitir certificados que serão usados em produção, ou seja, em ambientes de produção real acessados por usuários finais.

- Os certificados emitidos neste ambiente são submetidos aos limites de emissão normais do Let's Encrypt.

- O limite atual do Let's Encrypt para emissão de certificados é de 50 certificados por conjunto de domínios por semana. Esse limite inclui tanto os certificados emitidos automaticamente quanto aqueles emitidos manualmente através de ferramentas como o cert-manager.

- É importante usar este ambiente para certificados destinados a produção real, para garantir que os certificados estejam sujeitos aos mesmos requisitos e restrições que os certificados de produção normais.

2. **Let's Encrypt Staging (letsencrypt-staging):**

- Este ambiente é destinado ao desenvolvimento e teste de configurações de certificados.

- Os certificados emitidos neste ambiente são totalmente funcionais e usam a mesma tecnologia que os certificados de produção, mas não são confiáveis pelos navegadores ou outros aplicativos.

- O ambiente de preparo é útil para testar configurações, automatizações e integrações de certificados sem afetar os limites de emissão do ambiente de produção.

- Os limites de emissão no ambiente de preparo são muito mais altos do que no ambiente de produção. Isso permite que os desenvolvedores testem suas configurações e scripts de automação sem se preocuparem em atingir os limites de emissão normais do Let's Encrypt.

- Como os certificados de preparo não são confiáveis, eles são adequados apenas para ambientes de teste e desenvolvimento.

Em resumo, o ambiente de produção do Let's Encrypt (letsencrypt-production) é para certificados destinados a uso em produção real, enquanto o ambiente de preparo (letsencrypt-staging) é para testes e desenvolvimento de configurações de certificados sem afetar os limites de emissão do ambiente de produção.

#### Instalando e configurando o Cert-Manager.

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.0/cert-manager.yaml
```

> Versão: 1.15.0 [Cert-Manager Instalação kubectl apply](https://cert-manager.io/docs/installation/kubectl/)

Vamos verificar se o pod está rodando.

```bash
kubectl get pods -n cert-manager
```

ou

```bash
kubectl get all -n cert-manager
```

#### Configurando o Issuer que usa ACME para obter certificados de Let's Encrypt [Docs Issuer ACME](https://cert-manager.io/docs/configuration/acme/)

Criando um Issuer `staging_issuer.yaml`

```yaml
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@example.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          ingressClassName: nginx
```

Criando o ClusterIssuer `production_issuer.yaml`

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@example.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          ingressClassName: ngin


