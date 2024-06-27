# autodevops-platform-manifests
This repo holds the manifests required to execute CD part of autodevops workflow

# End User
## Pre-Requisites
```
apiVersion: pipelinesascode.tekton.dev/v1alpha1
kind: Repository
metadata:
  name: rh-hackathon-2024-autodevops-app
  namespace: demo
spec:
  url: <app_source_url> # Repo URL of the application ex: https://github.com/rh-hackathon-2024/autodevops-app
```

```
---
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  annotations:
    argocd-image-updater.argoproj.io/alias1.update-strategy: digest
    argocd-image-updater.argoproj.io/image-list: alias1=<image_to_be_monitored> # quay.io/rh-ee-anataraj/hello-world:0.1
    argocd-image-updater.argoproj.io/write-back-method: git
    argocd-image-updater.argoproj.io/git-branch: main
  name: <app_source_url_repo_name> #autodevops-app
  namespace: openshift-gitops # either argocd
spec:
  project: default
  destination:
    namespace: demo
    server: https://kubernetes.default.svc
  source:
    kustomize:
      images:
      - <image_to_be_pushed> #quay.io/rh-ee-anataraj/hello-world:0.1
    path: hello-world/
    repoURL: git@github.com:rh-hackathon-2024/autodevops-openshift-manifests.git
    targetRevision: HEAD
  syncPolicy:
    # automated:
      # prune: true
      # selfHeal: true
    syncOptions:
    - CreateNamespace=true
```
Create a secret to push image to quay
```
kubectl create secret generic regred --from-file <path_of_the_config.json_file> -n demo
```
