# Using the Helm Chart via Argo CD Application

This method is often preferred for managing configurations like TLS settings or resource limits through values.yaml files.

## Add the Helm repository to Argo CD

First, ensure Argo CD is aware of the Helm chart repository. You can do this via the UI in **Settings > Repositories** or using the CLI:

```bash
argocd repo add
```

```bash
https://kubernetes-sigs.github.io/metrics-server/
```

```bash
 --name metrics-server-helm
```

## Create an Argo CD Application manifest

Define the Application resource, specifying the Helm chart details.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: metrics-server-helm
  namespace: argocd
spec:
  destination:
    namespace: kube-system
    server: https://kubernetes.default.svc
  source:
    repoURL: 
        https://kubernetes-sigs.github.io/metrics-server/

    chart: metrics-server
    targetRevision: v3.x.x # Use the latest stable version (e.g., v3.12.0)
    helm:
      values: |
        args:
          - --kubelet-insecure-tls # Use this flag if you don't have proper TlS certificates configured for Kubelet
  project: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

## Apply the Argo CD Application and monitor the sync process as described in Method 1.

## Verification

Once installed and synced, the Metrics Server pod should be running and healthy in the `kube-system` namespace. You can verify the installation by checking node and pod metrics:

```bash
kubectl get pods -n kube-system | grep metrics-server
kubectl top nodes
kubectl top pods --all-namespaces
```

If these commands return CPU and memory usage data, the Metrics Server is installed and functioning correctly.
