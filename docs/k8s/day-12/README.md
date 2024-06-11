# PICK - Programa Intensivo em Containers e Kubernetes 2024 :computer:

## Day-12 Kubernetes :rocket:

## Cert-Manager, Annotations e Labels 

### Cert-manager

Cert-manager é uma ferramenta de código aberto que ajuda a gerenciar certificados SSL/TLS em clusters Kubernetes. Ele automatiza a aquisição, renovação e gerenciamento de certificados de diversas fontes, como Let's Encrypt, HashiCorp Vault, Venafi e outras autoridades certificadoras (CAs).

#### Funcionalidades Principais

1. **Automatização de Certificados:** Cert-manager permite que você configure regras para solicitar e renovar certificados automaticamente, garantindo que os serviços em seu cluster Kubernetes estejam sempre usando certificados válidos.

2. **Integração com Várias CAs:** Suporta várias CAs, como Let's Encrypt, ACME (Automatic Certificate Management Environment) e outras, oferecendo flexibilidade na escolha do provedor de certificados.

3. **Suporte a Diversos Tipos de Certificados:** Além de certificados padrão, o cert-manager pode gerenciar certificados wildcard (curinga) e certificados autoassinados.

4. **Configuração via Kubernetes CRDs:** Utiliza Custom Resource Definitions (CRDs) do Kubernetes para gerenciar certificados e emissoras (issuers). As CRDs permitem definir como e quando os certificados devem ser solicitados e renovados.

5. **Renovação Automática:** Cert-manager monitora a validade dos certificados e os renova automaticamente antes que expirem, reduzindo o risco de interrupções devido a certificados expirados.

6. **Suporte a Integrações Complexas:** Pode ser configurado para trabalhar com Ingress controllers, permitindo a configuração automática de HTTPS para serviços expostos externamente.




