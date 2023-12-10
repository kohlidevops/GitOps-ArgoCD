# GitOps-ArgoCD

## What is GitOps?
GitOps uses GIT as a single source of truth to deliver applications and infrastructure.

### Why GitOps?
If your source code has a mechanism of tracking, then why your deployment should not have a mechanism of tracking. So if your CI has a proper Git integrated approach, then why your CD doesn't have a proper Git integrated approach. This is how GitOps came.

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/1a864f7d-bacd-4dd8-aef8-2308b8e2223a)

When a DevOps Engineer updates the yaml file (Kubernetes deployment yaml file) in GitHub repo, this file should be validated by another DevOps engineer. After the PR validation, its merged to GitHub repository. Once code has changed in GitHub repo, the GitOps controller (Probably ArgoCD or Flux) will read the yaml files and deploys on to kubernetes cluster.

## GitOps Principles

### 1. Declarative
GitOps repository must have a declarative manifest file - Whatever you have seen in Git repository is the same configuration that is deployed on K8 cluster.

### 2. Versioned and Immutable
When you track resources in Git then one of the main thing that your changes are Versions and your changes are Immutable. (We can use S3 as GitOps repository)

### 3. Pulled Automatically
Agents known as GitOps controller (ArgoCD) automatically pull the code from repository.

### 4. Continuously Reconciled
If someone going to change anything in K8 cluster, then GitOps controller don't allow (means if changed anything then GitOps will override the changes on K8 cluster again) any changes in K8 cluster directly. Because GitOps using single source of truth to deliver applications. If you want to change something, then changes should be updated over GitOps repository.

## Is GitOps for Kubernetes only?

By the Principle, the Answer is "NO".
However, the popular GitOps tools like ArgoCD and Flux targets the Kubernetes.

## GitOps Advantages

1. Security - Its more secure. If any hackers did any changes on K8 cluster, then GitOps automatically override the changes. (Not prevent, But will override)
2. Versioning - Tracking a changes in GitOps repo
3. Auto upgrades -
4. Auto healing of any unwanted changes -
5. Continuous Reconciliation


