# GitOps-ArgoCD

## What is GitOps?
GitOps uses GIT as a single source of truth to deliver applications and infrastructure.

### Why GitOps?
If your source code has a mechanism of tracking, then why your deployment should not have a mechanism of tracking. So if your CI has a proper Git integrated approach, then why your CD doesn't have a proper Git integrated approach. This is how GitOps came.

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/1a864f7d-bacd-4dd8-aef8-2308b8e2223a)

When a DevOps Engineer updates the yaml file (Kubernetes deployment yaml file) in GitHub repo, this file should be validated by another DevOps engineer. After the PR validation, its merged to GitHub repository. Once code has changed in GitHub repo, the GitOps controller (Probably ArgoCD or Flux) will read the yaml files and deploys on to kubernetes cluster.
