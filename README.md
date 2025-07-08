Atenção: 

Este repositório é tirado do repositório original [GoogleCloudPlataform/microservices-demo](https://github.com/GoogleCloudPlataform/microservices-demo). Para criar um fork do repositório original, siga para este [passo](#criando-um-fork) 

 

# O projeto 

Este repositório consta o estudo aplicado a um microserviço utilizando o argoCD. O projeto se divide em: 

1. [Introdução](#introdução) 

1. [Pré requisitos](#pré-requisitos) 

1. [Instalando os pré requisitos](#instalando-os-pre-requisitos) 

     

    3.1. [rancher desktop, kubectl e docker](#rancher-desktop-kubectl-e-docker) 

 

    3.2. [Git](#git) 

 

    3.3. [ArgoCD](#argocd) 

 

1. [Criando um fork](#criando-um-fork) 

 

    4.1. [Criando o fork](#criando-o-fork) 

 

    4.2. [Criando um novo repositório](#criando-um-novo-repositório) 

1. [Instalando o ArgoCD no cluster](#instalando-o-argocd-no-cluster) 

1. [Acessando o ArgoCD](#acessando-o-argocd) 

1. [Criar o App no ArgoCD](#criar-o-app-no-argocd) 

 

    7.1. [Criando um PAT](#criando-um-pat) 

1. [Acessar o front-end](#acessar-o-front-end) 

 

# Introdução 

Este repositório tem como objetivo demonstrar a implementação de um microserviço utilizando o ArgoCD para gerenciamento de entrega contínua. O ArgoCD é uma ferramenta poderosa que permite a implementação de práticas GitOps em ambientes Kubernetes, facilitando a automação e o controle de versões das aplicações. 

# Pré requisitos 

- Rancher Desktop 

- Kubectl 

- ArgoCD 

- Conta no GitHub com repositório público 

- Git instalado 

- Docker 

 

# Instalando os pré requisitos 

## Rancher Desktop, kubectl e docker 

Para instalar o Rancher Desktop, siga as instruções no [site oficial](https://rancherdesktop.io). Instale a versão correta para seu sistema operacional e siga atentamente as instruções de instalação, pois o passo de instalar o Kubernetes é fundamental para o funcionamento do ArgoCD. Uma vez instalado o rancher verifique se o kubectl e o docker estão funcionando corretamente utilizando o comando: 

 

```bash 

kubectl get nodes 

``` 

 

```bash 

docker --version 

``` 

 

Caso os comandos não retornem erros, significa que o Rancher Desktop, kubectl e Docker estão instalados corretamente. 

 

## Git 

Para instalar o Git, siga as instruções no [site oficial](https://git-scm.com/downloads). Após a instalação, verifique se o Git está funcionando corretamente utilizando o comando: 

 

```bash 

git --version 

``` 

 

Caso o comando retorne a versão do Git, significa que ele está instalado corretamente. 

 

## ArgoCD 

Este processo será realizado [neste passo](#instalando-o-argocd-no-cluster). 

 

# Criando um fork 

## Criando o fork 

Existem diversas formas de criar um fork de um repositório. Primeiramente, acesse o seu github, ou crie sua conta, e após isto acesse o [repositório original](https://github.com/GoogleCloudPlatform/microservices-demo). 

Clique no botão "Fork" localizado no canto superior direito da página do repositório. Isso criará uma cópia do repositório original na sua conta do GitHub. 

![alt text](images/image.png) 

 

A mais simples, porém, que necessita de mais afinidade com o prompt de comando / terminal é usar os comandos: 

```bash 

git clone https://github.com/SEU_USUARIO/NOME_DO_REPOSITORIO 

cd microservices-demo 

git remote add upstream https://github.com/USUARIO_ORIGINAL/NOME_DO_REPOSITORIO 

``` 

Assim você poderá realizar o próximo passo. 

 

## Criando um novo repositório 

É possível criar um novo repositório e copiando o arquivo manifesto YAML necessario para a prática realizando os seguintes comandos: 

```bash 

mkdir <nome_do_novo_repositorio> 

cd <nome_do_novo_repositorio> 

git init 

mkdir gitops-microservice/k8s 

cp <caminho_do_diretorio_release>/kubernets_manifest.yaml .<nome_do_repositorio>/gitops-microservice/k8s/online-boutique.yaml 

git add . 

git commit -m "Adicionando o manifesto do microserviço" 

git remote add origin <url_do_seu_repositorio> 

git push -u origin main 

``` 

 

Seu novo repositório será criado e apto para ser utilizado no ArgoCD. 

# Instalando o ArgoCD no cluster 

Para instalar o ArgoCD no cluster Kubernetes, execute os seguintes comandos: 

 

```bash 

kubectl create namespace argocd 

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml 

``` 

Isso criará um namespace chamado `argocd` e aplicará os manifests necessários para instalar o ArgoCD no cluster. 

 

# Acessando o ArgoCD 

Para acessar o ArgoCD, você precisará expor o serviço do ArgoCD para fora do cluster. Execute o seguinte comando: 

 

```bash 

kubectl port-forward svc/argocd-server -n argocd 8080:443 

``` 

 

* Caso ocorra o erro `error: unable to forward port because pod is not running. Current status=Pending` siga estes passos: 

Abra o Rancher Desktop e clique em preferences: 

![alt text](images/image-1.png) 

Selecione a opção "Container Engine" > "allowed Images". 

Caso esteja igual a a imagem abaixo, instale a versão 1.19.1 de acordo com o sistema operacional 

![alt text](images/image-2.png) 

adicione a imagem quay.io clicando em (+) e adicionando o nome de acordo com a figura abaixo: 

![alt text](images/image-3.png) 

e faça o login no quay.io (crie um usuário no red hat se necessário) usando o código 

Outra tentativa é simplesmente desmarca a opção enable  

![alt text](images/image20.png) 

e verificar se a aplicação é executada após deleção do namespace e reinício do kubernetes 

![alt text](images/image-21.png) 

```bash 

docker login quay.io 

``` 

faça o processo de port-forward novamente 

 

Caso o prompt de comando/terminal fique realizando a tarefa, o ArgoCD estará acessível em `http://localhost:8080`. Abra o navegador e acesse essa URL. 

 

Abra uma nova aba de prompt/terminal e digite o comando 

```bash 

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d 

``` 

no linux ou macOS, ou  

```powershell 

$base64String =kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" 

``` 

e após isto 

```powershell 

[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($base64String)) 

``` 

no windows para obter a senha inicial do usuário `admin`. Use `admin` como nome de usuário e a senha obtida no comando acima para fazer login no ArgoCD. 

 

Caso tudo tenha funcionado corretamente, a tela aparecerá desta maneira: 

![alt text](images/image-4.png) 

 

# Criar o App no ArgoCD 

 

Para criar um novo aplicativo no ArgoCD, siga os seguintes passos: 

Clique em "settings" e após isto em "Repositories" 

![alt text](images/image-5.png) 

Clique em "Connect Repo" 

![alt text](images/image-6.png) 

Preencha os campos com as informações do seu repositório: 

- método de conexão: HTTP/HTTPS 

- type: Git 

- URL do repositório: <URL_DO_REPOSITORIO_GIT> 

- project: <nome_do_projeto> 

- username: <seu_usuario_github> 

- password: <[um PAT](#criando-um-pat)> 

 

Após isto, clique em "Connect" para conectar o repositório ao ArgoCD. 

![alt text](images/image-11.png) 

Caso esteja tudo correto, aparecerá seu repositório de acordo com a imagem 

![alt text](images/image-12.png) 

 

Iremos criar agora a aplicação. Na lateral, clique em "Applications" e depois em "New Application". 

![alt text](images/image-13.png) 

Em seguida preencha os campos necessários: 

- Application name: <nome_da_aplicação> 

- Projeto: <nome_do projeto> 

- opções: 

    - PRUNE RESOURCES 

    - SELF HEAL 

    - SET DELETION FINALIZER 

    - AUTO-CREATE NAMESPACE 

![alt text](images/image-14.png) 

- Repositório: <nome_do_repositório> 

- Caminho: gitops-microservice/k8s 

- Cluster: in-cluster 

- Namespace: default 

![alt text](images/image-15.png) 

 

Ou selecione a opção e edite conforme os dados que necessite completar: 

```yaml 

apiVersion: argoproj.io/v1alpha1 

kind: Application 

metadata: 

  name: online-boutique 

  finalizers: 

    - resources-finalizer.argocd.argoproj.io 

spec: 

  destination: 

    name: in-cluster 

    namespace: online-boutique 

  source: 

    path: ./gitops-microservices/k8s 

    repoURL: <repositorio_git> 

    targetRevision: main 

  sources: [] 

  project: default 

  syncPolicy: 

    automated: 

      prune: true 

      selfHeal: true 

    syncOptions: 

      - CreateNamespace=true 

``` 

Caso esteja dessincronizado, clique em "Sync" para sincronizar. Aguarde um momento até o status seja alterado: 

![alt text](images/image-16.png) 

 

## criando um PAT 

Caso não tenha um Personal Access Token (PAT) com escopo repo siga os seguintes passos: 

1. Acesse o GitHub e vá para as configurações da sua conta. 

![alt text](images/image-7.png) 

2. Clique em "Developer settings" no menu lateral ao final do menu. 

![alt text](images/image-8.png) 

3. Clique em "Personal access tokens" e depois em "Tokens (classic)". 

![alt text](images/image-9.png) 

4. Clique em "Generate new token (classic)". 

5. Preencha os campos necessários: 

   - Nome: <nome_do_token> 

   - Expiração: Escolha uma data de expiração adequada. 

   - Escopos: Selecione os escopos necessários, como `repo` para acesso ao repositório. 

   ![alt text](images/image-10.png) 

   clique em "Generate token" para criar o token. 

6. Copie o token gerado e cole no campo "password" do ArgoCD. 

 

 

# Acessar o front-end 

Aguarde o processo ser iniciado de acordo com a imagem 

![alt text](images/image-18.png) 

 

 Caso tenha dificuldades de encontrar loadbalancer utilize o seguinte comando 

 ```powershell 

  kubectl get services --all-namespaces | Select-String "frontend-external" 

 ``` 

 Neste exemplo, o namespace é `online-boutique`, assim usamos o comando  

 ```powershell 

 kubectl port-forward svc/frontend-external 80:80 -n online-boutique 

 ``` 

Caso não possua outras aplicações conflitantes aparecerá uma mensagem do tipo: 

```powershell 

Forwarding from localhost:80 -> 8080 

``` 

Caso possua alguma aplicação usando a porta será mostrado na tela 

```powershell 

Forwarding from 127.0.0.1:80 -> 8080 

``` 

 

Assim temos a aplicação que gostaríamos de visualizar 

![alt text](images/image-19.png) 

 

Caso não queira usar a porta 80 por tantas dificuldades, utilize uma porta que não esteja usada 

 

# Customizando o manifesto 

 

Podemos pegar um exemplo dentro do online-boutique.yaml: 

```yaml 

apiVersion: apps/v1 

kind: Deployment 

metadata: 

  name: loadgenerator 

  labels: 

    app: loadgenerator 

spec: 

  selector: 

    matchLabels: 

      app: loadgenerator 

  replicas: 1 

  template: 

``` 

Para modificar este deployment basta alterar para  

```yaml 

apiVersion: apps/v1 

kind: Deployment 

metadata: 

  name: loadgenerator 

  labels: 

    app: loadgenerator 

spec: 

  selector: 

    matchLabels: 

      app: loadgenerator 

  replicas: 3 

  template: 

``` 

 

Podemos também fixar uma versão do pod. No arquivo original: 

```yaml 

      - name: server 

        securityContext: 

          allowPrivilegeEscalation: false 

          capabilities: 

            drop: 

              - ALL 

          privileged: false 

          readOnlyRootFilesystem: true 

        image: us-central1-docker.pkg.dev/google-samples/microservices-demo/emailservice:v0.10.3 

        ports: 

        - containerPort: 8080 

``` 

para uma versão mais antiga: 

```yaml 

      - name: server 

        securityContext: 

          allowPrivilegeEscalation: false 

          capabilities: 

            drop: 

              - ALL 

          privileged: false 

          readOnlyRootFilesystem: true 

        image: us-central1-docker.pkg.dev/google-samples/microservices-demo/emailservice:v0.8.3 

        ports: 

        - containerPort: 8080 

``` 

 

Outra forma seria adicionar variáveis de ambiente: 

```yaml 

- name: server 

        securityContext: 

          allowPrivilegeEscalation: false 

          capabilities: 

            drop: 

              - ALL 

          privileged: false 

          readOnlyRootFilesystem: true 

        env:  

          - name: FEATURE_FLAG_NEW_UI 

            value: "true" 

        image: us-central1-docker.pkg.dev/google-samples/microservices-demo/emailservice:v0.10.3 

        ports: 

        - containerPort: 8080 

``` 

onde a nova chave `FEATURE_FLAG_NEW_UI` recebe o valor `TRUE`. estas variáveis servem para passar de forma simples valores não importantes para segurança e facilita na execução do código. Outra forma de utilizar variáveis que não necessitem de serem guardadas com grande segurança são os arquivo ConfigMap. 

 

Os arquivos armazenam as variaveis e podem ser passadas das seguintes formas: 

 

1. Criamos um novo yaml: 

Arquivo global-config.yaml: 

```yaml 

apiVersion: v1 

kind: ConfigMap 

metadata: 

  name: app-feature-flags 

  namespace: online-boutique 

data: 

  FEATURE_FLAG_NEW_UI: "true" 

``` 

e aplique o configMap à execução da aplicação 

```powershell 

kubectl apply -f global-config.yaml -n online-boutique 

``` 

 

2. Adicionar a chamada do arquivo dentro do yaml original: 

Seria necessario além de criar o arquivo de forma correta, a chamada dela na forma de 

```yaml 

- name: server 

        securityContext: 

          allowPrivilegeEscalation: false 

          capabilities: 

            drop: 

              - ALL 

          privileged: false 

          readOnlyRootFilesystem: true 

        image: us-central1-docker.pkg.dev/google-samples/microservices-demo/emailservice:v0.10.3 

        ports: 

        - containerPort: 8080 

        envFrom: 

        - configMapRef: 

            name: app-feature-flags 

``` 
Há outras formas de customização do yaml, incluindo diversas contidas no yaml original, como memória mínima, etc. Porém o mais correto seria verificar a [documentação](https://argo-cd.readthedocs.io/en/stable/) do argocd ou utilizando o [kustomize](https://kustomize.io/) para diversas modificações  