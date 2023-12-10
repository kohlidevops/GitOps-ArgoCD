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

## Popular GitOps Tools in market

1. ArgoCD
2. Flux
3. JenkinsX
4. Spinnaker

## GitOps Architecture

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/b2b9bf72-8ac3-4fcb-a4e8-959f7b8d7fe9)

Repo Server - You have a repo server which is talking to Git.

Application Controller -You have a application controller which is talking to kubernetes.

These Repo server and application controller gets the information and compare the state.

Application controller get the information from Repo server and it always tries to see if the state in Git and Kubernetes is same or not.

API Server - You have a API server which is used for UI / CLI authentication.

DEX - You have a DEX which has the SSO capability.

Redis - Which is used for cache - Why we beed caching? Because these are the stateful sets and these application has to be stateful sets and they have to cache the information.

### Installation ways

1. YAML manifests
2. Helm
3. Kubernetes Operator

## Handson

### To setup minikube on local system

I just used ubuntu22 with t3.medium instance in AWS Cloud. In order to work with ArgoCD, we have a kubernetes cluster environment.

SSH to machine and install below commands to install minikube and kubectl on ubuntu-22.

#### To update your machine

  sudo apt-get update -y
  sudo apt-get upgrade -y

#### To install docker

  sudo apt install ca-certificates curl gnupg wget apt-transport-https -y
  sudo install -m 0755 -d /etc/apt/keyrings
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
  sudo chmod a+r /etc/apt/keyrings/docker.gpg
  echo \
    "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  sudo apt update
  sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

#### Add your local user to docker group so that your local user run docker commands without sudo

  sudo usermod -aG docker $USER
  newgrp docker

#### Download and Install minikube

  curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  sudo install minikube-linux-amd64 /usr/local/bin/minikube
  minikube version

#### Install kubectl tool

  curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
  chmod +x kubectl
  sudo mv kubectl /usr/local/bin/
  kubectl version -o yaml

#### To start minikube cluster

  minikube start --driver=docker
  minikube status
