<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&height=200&color=gradient&customColorList=0:1F9BD4,50:2E75B6,100:16265F&text=projetoGitOps&fontColor=ffffff&fontSize=42&fontAlignY=35&desc=GitOps%20com%20ArgoCD%20%2B%20Microservi%C3%A7os%20%7C%20Estudo%20Aplicado&descAlignY=55&descSize=16&animation=twinkling" width="100%" />

[![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![ArgoCD](https://img.shields.io/badge/ArgoCD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)](https://argoproj.github.io/cd/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://docker.com)
[![GitOps](https://img.shields.io/badge/GitOps-F05032?style=for-the-badge&logo=git&logoColor=white)](https://www.gitops.tech/)

[![Google Cloud](https://img.shields.io/badge/baseado%20em-Google%20Cloud%20Demo-4285F4?style=flat-square&logo=googlecloud&logoColor=white)](https://github.com/GoogleCloudPlatform/microservices-demo)
[![Microservices](https://img.shields.io/badge/arquitetura-microserviços-blueviolet?style=flat-square)]()
[![Auto-Sync](https://img.shields.io/badge/ArgoCD-auto--sync-orange?style=flat-square)]()
[![Status](https://img.shields.io/badge/status-ativo-brightgreen?style=flat-square)]()

</div>

---

## 📋 Sobre o Projeto

O **projetoGitOps** é um **estudo aplicado de GitOps** utilizando ArgoCD para orquestrar o deploy de uma aplicação de microserviços em Kubernetes. O projeto é baseado no demo de microserviços do **Google Cloud Platform** (Online Boutique), adaptado para demonstrar o fluxo GitOps em um ambiente local com Rancher Desktop.

O objetivo principal é entender e demonstrar na prática os conceitos de: deploy declarativo, auto-sync, rollback automático e reconciliação contínua — pilares do GitOps com ArgoCD.

---

<div align="center">

## 🛠️ Stack de Tecnologias

[![My Skills](https://skillicons.dev/icons?i=kubernetes,docker,git,github,linux&theme=dark)](https://skillicons.dev)

</div>

| Ferramenta | Versão | Finalidade |
|---|---|---|
| **Rancher Desktop** | 1.x+ | Kubernetes local (substitui Docker Desktop) |
| **kubectl** | 1.28+ | CLI Kubernetes |
| **Docker** | 24+ | Build e execução de containers |
| **ArgoCD** | 2.x | Operador GitOps |
| **Git** | 2.x | Controle de versão e fonte da verdade |

---

## 📁 Estrutura do Repositório

```
projetoGitOps/
│
├── gitops-microservices/
│   └── k8s/
│       └── online-boutique.yaml    # Manifest completo dos microserviços
│
└── docs/
    ├── 01_rancher_desktop.png      # Instalação Rancher Desktop
    ├── 02_argocd_install.png       # Instalação ArgoCD
    ├── 03_argocd_ui.png            # Interface do ArgoCD
    ├── 04_app_create.png           # Criando Application
    ├── 05_sync_status.png          # Status de sincronização
    ├── ...                         # + 16 imagens documentando o processo
    └── 21_rollback.png             # Demonstração de rollback
```

---

## ⚙️ Pré-requisitos e Instalação

<details>
<summary>🖥️ <strong>1. Rancher Desktop</strong></summary>

<br>

O Rancher Desktop é uma alternativa gratuita ao Docker Desktop que inclui Kubernetes integrado:

1. Baixe em: [rancherdesktop.io](https://rancherdesktop.io/)
2. Instale e aguarde a inicialização do cluster Kubernetes
3. Verifique a instalação:

```bash
kubectl get nodes
# NAME                   STATUS   ROLES                  AGE   VERSION
# rancher-desktop        Ready    control-plane,master   1m    v1.28.x
```

</details>

<details>
<summary>🔧 <strong>2. kubectl</strong></summary>

<br>

```bash
# Linux
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Verificar
kubectl version --client
```

</details>

<details>
<summary>🔄 <strong>3. ArgoCD no Cluster</strong></summary>

<br>

```bash
# Criar namespace do ArgoCD
kubectl create namespace argocd

# Instalar ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Aguardar pods ficarem prontos
kubectl wait --for=condition=Ready pods --all -n argocd --timeout=120s

# Expor o ArgoCD UI localmente
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Obter senha inicial do admin
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```

Acesse: `https://localhost:8080` | Usuário: `admin`

</details>

---

## 🚀 Fluxo GitOps — Passo a Passo

```
┌────────────────────────────────────────────────────────────────────┐
│                   FLUXO GITOPS COMPLETO                            │
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  PASSO 1: Fork do repositório de microserviços               │  │
│  │  github.com/GoogleCloudPlatform/microservices-demo           │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  PASSO 2: Criar repositório de manifests (este repo)         │  │
│  │  Copiar k8s/online-boutique.yaml para gitops-microservices/  │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  PASSO 3: Instalar e configurar ArgoCD                       │  │
│  │  Criar Application apontando para este repositório           │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                              │                                     │
│                              ▼                                     │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  PASSO 4: ArgoCD sincroniza manifests → deploya no cluster   │  │
│  │  Auto-sync habilitado: qualquer mudança no Git é aplicada    │  │
│  └──────────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────────┘
```

---

### Configuração da Application no ArgoCD

```yaml
# argocd-app.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: online-boutique
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/fassir/projetoGitOps
    targetRevision: HEAD
    path: gitops-microservices/k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: online-boutique
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

```bash
kubectl apply -f argocd-app.yaml
```

---

## 🏪 Online Boutique — Microserviços

O projeto demonstra o deploy de uma loja online completa composta por múltiplos microserviços:

| Microserviço | Linguagem | Função |
|---|---|---|
| `frontend` | Go | Interface web da loja |
| `cartservice` | C# | Gerenciamento do carrinho |
| `productcatalogservice` | Go | Catálogo de produtos |
| `checkoutservice` | Go | Processamento de pedidos |
| `paymentservice` | Node.js | Simulação de pagamentos |
| `emailservice` | Python | Envio de confirmações |
| `shippingservice` | Go | Cálculo de frete |
| `recommendationservice` | Python | Recomendações de produtos |
| `adservice` | Java | Serviço de anúncios |
| `currencyservice` | Node.js | Conversão de moedas |
| `loadgenerator` | Python | Gerador de carga para testes |

---

## 🔍 Verificando o Deploy

```bash
# Status de todos os pods
kubectl get pods -n online-boutique

# Verificar services
kubectl get svc -n online-boutique

# Acessar o frontend
kubectl port-forward svc/frontend -n online-boutique 8081:80

# Ver logs de um serviço específico
kubectl logs -l app=frontend -n online-boutique

# Status no ArgoCD
argocd app get online-boutique
```

---

<details>
<summary>↩️ <strong>Demonstrando Rollback Automático</strong></summary>

<br>

Um dos recursos mais poderosos do GitOps é o **rollback automático via Git**:

```bash
# Simular uma mudança problemática
# Edite o manifest com uma imagem inválida e faça push

# ArgoCD detecta a mudança e tenta aplicar
# O Kubernetes falha ao criar o pod com imagem inválida

# Para rollback: reverter o commit no Git
git revert HEAD
git push

# ArgoCD detecta o revert e sincroniza de volta
# O estado desejado (Git) volta ao normal
# O cluster é reconciliado automaticamente
```

**Resultado:** O cluster volta ao estado anterior sem intervenção manual, apenas com operações Git.

</details>

---

## 📸 Documentação Visual

O repositório inclui **21 capturas de tela** documentando todo o processo, desde a instalação das ferramentas até a demonstração do rollback automático. Esta documentação visual serve como guia de referência para replicar o ambiente.

---

## 👤 Autor

<div align="center">

| | |
|---|---|
| **Nome** | Fabio Piassi |
| **LinkedIn** | [linkedin.com/in/fabio-piassi](https://linkedin.com/in/fabio-piassi) |
| **GitHub** | [github.com/fassir](https://github.com/fassir) |

</div>

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&section=footer&height=120&color=gradient&customColorList=0:16265F,50:2E75B6,100:1F9BD4" width="100%" />

*Estudo aplicado de GitOps com ArgoCD, microserviços e Kubernetes*

</div>
