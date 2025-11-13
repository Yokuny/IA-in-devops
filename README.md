# Setup Inicial - Deploy Automático com GitHub Actions

Este guia mostra os passos necessários para configurar o deploy automático da aplicação na DigitalOcean usando Kubernetes e GitHub Actions.

## 📋 Pré-requisitos

- Conta na DigitalOcean
- Docker instalado localmente
- kubectl instalado
- doctl (CLI da DigitalOcean) instalado

## 🚀 Passo 1: Criar Infraestrutura na DigitalOcean

### 1.1 Gerar Token de API

1. Acesse: https://cloud.digitalocean.com/account/api/tokens/new
2. Crie um token com permissões de **read** e **write**
3. Copie e guarde o token em local seguro

### 1.2 Autenticar doctl

```bash
doctl auth init
# Cole o token quando solicitado
```

### 1.3 Criar Container Registry

```bash
doctl registry create conversao-distancia-registry
doctl registry login
```

### 1.4 Criar Cluster Kubernetes

```bash
doctl kubernetes cluster create conversao-distancia-cluster \
  --region nyc1 \
  --version latest \
  --node-pool "name=worker-pool;size=s-2vcpu-2gb;count=2"

# Configurar kubectl
doctl kubernetes cluster kubeconfig save conversao-distancia-cluster
```

## ⚙️ Passo 2: Configurar Variáveis no GitHub

Vá em: **Settings → Environments → docker** (crie o environment se não existir)

### Environment Variables (vars):

| Nome | Valor |
|------|-------|
| `REGISTRY_NAME` | `conversao-distancia-registry` |
| `CLUSTER_NAME` | `conversao-distancia-cluster` |

### Environment Secrets (secrets):

| Nome | Valor |
|------|-------|
| `DIGITALOCEAN_ACCESS_TOKEN` | Seu token da DigitalOcean |

## 🎯 Passo 3: Primeiro Deploy Manual

```bash
# Build da imagem
docker build --platform linux/amd64 -t registry.digitalocean.com/conversao-distancia-registry/conversao-distancia:latest .

# Push para registry
docker push registry.digitalocean.com/conversao-distancia-registry/conversao-distancia:latest

# Configurar imagePullSecret
doctl registry kubernetes-manifest | kubectl apply -f -
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "registry-conversao-distancia-registry"}]}'

# Deploy
kubectl apply -f manifest.yaml

# Verificar
kubectl get pods
kubectl get svc
```

## ✅ Pronto! Deploy Automático Configurado

Agora, **a cada commit na branch `main`**, o GitHub Actions vai automaticamente:

1. ✅ Instalar dependências Python
2. ✅ Executar testes
3. ✅ Build da imagem Docker
4. ✅ Push para o Container Registry da DigitalOcean
5. ✅ Deploy no cluster Kubernetes
6. ✅ Verificar status do deployment
7. ✅ Mostrar IP do LoadBalancer

## 📝 Workflow Diário

```bash
# Fazer alterações no código
git add .
git commit -m "feat: nova funcionalidade"
git push origin main

# A pipeline executa automaticamente!
# Acompanhe em: https://github.com/seu-usuario/seu-repo/actions
```

## 🔍 Comandos Úteis

```bash
# Ver logs da aplicação
kubectl logs -l app=conversao-distancia -f

# Ver status dos pods
kubectl get pods

# Ver IP do LoadBalancer
kubectl get svc conversao-distancia

# Escalar aplicação
kubectl scale deployment conversao-distancia --replicas=3

# Restart da aplicação
kubectl rollout restart deployment conversao-distancia
```

## 💰 Custos Estimados

- Container Registry: $5/mês
- Kubernetes (2 nodes): ~$24/mês
- LoadBalancer: $12/mês
- **Total**: ~$41/mês

## 🆘 Troubleshooting

### Pipeline falha no login do registry
- Verifique se `DIGITALOCEAN_ACCESS_TOKEN` está configurado corretamente
- Confirme que o token tem permissões de read/write

### Pods não iniciam
```bash
kubectl describe pod <nome-do-pod>
kubectl logs <nome-do-pod>
```

### LoadBalancer sem IP
```bash
# Aguarde 1-2 minutos e verifique novamente
kubectl get svc conversao-distancia --watch
```

## 🗑️ Limpeza (Deletar Tudo)

```bash
kubectl delete -f manifest.yaml
doctl kubernetes cluster delete conversao-distancia-cluster
doctl registry delete conversao-distancia-registry
```
