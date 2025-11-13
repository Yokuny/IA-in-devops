# Deploy Manual - Criar Ambiente na DigitalOcean

Siga estes passos para criar o ambiente antes de usar a pipeline do GitHub Actions.

## Pré-requisitos

```bash
# Instalar doctl (macOS)
brew install doctl

# Instalar kubectl (se não tiver)
brew install kubectl
```

## Passo 1: Autenticar na DigitalOcean

```bash
# Gerar token em: https://cloud.digitalocean.com/account/api/tokens/new
doctl auth init
# Cole o token quando solicitado
```

## Passo 2: Criar Container Registry

```bash
# Criar registry
doctl registry create conversao-distancia-registry

# Verificar
doctl registry get

# Login no registry
doctl registry login
```

✅ **REGISTRY_NAME**: `conversao-distancia-registry`

## Passo 3: Criar Cluster Kubernetes

```bash
# Criar cluster (demora ~5 minutos)
doctl kubernetes cluster create conversao-distancia-cluster \
  --region nyc1 \
  --version latest \
  --node-pool "name=worker-pool;size=s-2vcpu-2gb;count=2;auto-scale=true;min-nodes=1;max-nodes=3"

# Configurar kubectl
doctl kubernetes cluster kubeconfig save conversao-distancia-cluster

# Verificar
kubectl get nodes
```

✅ **CLUSTER_NAME**: `conversao-distancia-cluster`

## Passo 4: Build e Push da Primeira Imagem

```bash
# Build da imagem
docker build --platform linux/amd64 -t registry.digitalocean.com/conversao-distancia-registry/conversao-distancia:latest .

# Push para o registry
docker push registry.digitalocean.com/conversao-distancia-registry/conversao-distancia:latest
```

## Passo 5: Configurar imagePullSecret no Cluster

```bash
# Criar secret para pull de imagens
doctl registry kubernetes-manifest | kubectl apply -f -

# Configurar serviceaccount default
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "registry-conversao-distancia-registry"}]}'
```

## Passo 6: Deploy da Aplicação

```bash
# Aplicar manifest
kubectl apply -f manifest.yaml

# Verificar deployment
kubectl get deployments
kubectl get pods
kubectl get svc
```

## Passo 7: Obter URL da Aplicação

```bash
# Aguardar LoadBalancer (pode demorar 1-2 minutos)
kubectl get svc conversao-distancia --watch

# Obter IP
kubectl get svc conversao-distancia -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Acesse: `http://<IP-DO-LOADBALANCER>`

## Passo 8: Configurar GitHub Actions

Vá em: **Settings → Environments → docker**

### Variables:
- `REGISTRY_NAME`: `conversao-distancia-registry`
- `CLUSTER_NAME`: `conversao-distancia-cluster`

### Secrets:
- `DIGITALOCEAN_ACCESS_TOKEN`: seu token da DigitalOcean

## Pronto!

Agora você pode fazer commits e a pipeline vai automaticamente:
1. Build da nova imagem
2. Push para o registry
3. Deploy no cluster
4. Verificar status

## Comandos Úteis

```bash
# Ver logs
kubectl logs -l app=conversao-distancia -f

# Escalar
kubectl scale deployment conversao-distancia --replicas=3

# Restart
kubectl rollout restart deployment conversao-distancia

# Deletar tudo
kubectl delete -f manifest.yaml
```
