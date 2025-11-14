# Configuração Atual do Projeto

## ✅ Status Atual

### Registry DigitalOcean
- **Nome**: `git-action`
- **Região**: `sfo2`
- **Imagem**: `registry.digitalocean.com/git-action/conversao-distancia:latest`
- **Status**: ✅ Imagem enviada com sucesso

### Arquivos Configurados
- ✅ `manifest.yaml` - usando `registry.digitalocean.com/git-action/conversao-distancia:latest`
- ✅ `Dockerfile` - configurado para build da aplicação
- ✅ `.github/workflows/main.yml` - pipeline CI/CD configurada

## 🔧 Próximos Passos

### 1. Criar Cluster Kubernetes

```bash
doctl kubernetes cluster create git-action-cluster \
  --region sfo2 \
  --version latest \
  --node-pool "name=worker-pool;size=s-2vcpu-2gb;count=2;auto-scale=true;min-nodes=1;max-nodes=3"
```

### 2. Configurar kubectl

```bash
doctl kubernetes cluster kubeconfig save git-action-cluster
kubectl get nodes
```

### 3. Configurar Variáveis no GitHub

Vá em: **Settings → Environments**

#### Environment: `docker` (para CI)
**Variables:**
- `REGISTRY_NAME`: `git-action`

**Secrets:**
- `DIGITALOCEAN_TOKEN`: seu token da DigitalOcean

#### Environment: `digitalocean` (para CD)
**Variables:**
- `CLUSTER_NAME`: `git-action-cluster`
- `REGISTRY_NAME`: `git-action`

**Secrets:**
- `DIGITALOCEAN_ACCESS_TOKEN`: seu token da DigitalOcean

### 4. Configurar imagePullSecret no Cluster

```bash
doctl registry kubernetes-manifest | kubectl apply -f -
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "registry-git-action"}]}'
```

### 5. Deploy Manual (Primeira Vez)

```bash
kubectl apply -f manifest.yaml
kubectl get pods
kubectl get svc
```

### 6. Obter IP do LoadBalancer

```bash
kubectl get svc conversao-distancia --watch
```

Aguarde até aparecer o EXTERNAL-IP, depois acesse: `http://<EXTERNAL-IP>`

## 🚀 Deploy Automático

Após configurar tudo acima, basta fazer commit e push:

```bash
git add .
git commit -m "feat: nova funcionalidade"
git push origin main
```

A pipeline vai automaticamente:
1. Build da imagem
2. Push para `registry.digitalocean.com/git-action/conversao-distancia:latest`
3. Deploy no cluster `git-action-cluster`
4. Mostrar IP do LoadBalancer

## 📊 Verificar Status

```bash
# Ver pods
kubectl get pods -l app=conversao-distancia

# Ver logs
kubectl logs -l app=conversao-distancia -f

# Ver service
kubectl get svc conversao-distancia

# Ver deployment
kubectl get deployment conversao-distancia
```

## 🔄 Comandos Úteis

```bash
# Escalar aplicação
kubectl scale deployment conversao-distancia --replicas=3

# Restart
kubectl rollout restart deployment conversao-distancia

# Ver histórico de deploys
kubectl rollout history deployment conversao-distancia

# Rollback
kubectl rollout undo deployment conversao-distancia
```

## 📝 Resumo das Configurações

| Item | Valor |
|------|-------|
| Registry | `git-action` |
| Cluster | `git-action-cluster` (a criar) |
| Região | `sfo2` |
| Imagem | `registry.digitalocean.com/git-action/conversao-distancia:latest` |
| Deployment | `conversao-distancia` |
| Service | `conversao-distancia` (LoadBalancer) |
| Porta | 80 → 8000 |
| Réplicas | 2 |
