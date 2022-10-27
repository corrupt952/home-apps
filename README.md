# Home k8s manifests

Repository for the home kubernetes cluster.

This repository manages the manifests of the home kubernetes cluster.

## Setup Argo CD

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm install argocd argo/argo-cd --create-namespace --namespace argocd
kubectl -n argocd port-forward deploy/argocd-server 8080:8080
argocd login \
    --insecure \
    --grpc-web \
    --username admin \
    --password "$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)" \
    localhost:8080
argocd app create argocd-config \
    --insecure \
    --grpc-web \
    --repo https://github.com/corrupt952/home-apps.git \
    --path argocd-config/base \
    --dest-namespace argocd \
    --dest-server https://kubernetes.default.svc \
    --sync-policy automated \
    --auto-prune \
    --revision main
```
