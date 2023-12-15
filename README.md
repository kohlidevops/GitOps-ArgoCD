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

#### Install ArgoCD using manifest file

    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

#### To check the status of kubernetes pods

    watch kubectl get pods -n argocd

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/e09aad15-2b65-48c4-8d82-158cd19fbeed)

#### Forwarding ports to Access ArgoCD

    kubectl port-forward svc/argocd-server -n argocd 8080:443 --address='0.0.0.0'
    or
    nohup kubectl port-forward svc/argocd-server -n argocd 8080:443 --address='0.0.0.0' &

#### To find the Login credentials

Default username is "admin"

To know the password, to execute below command

    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

#### To access the ArgoCD portal in browser

Navigate to browser and enter - http://ec2-instance-ip:8080

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/a51cfc2b-5f1a-4ae8-8139-ac5016ddcc12)

#### To change the default password

After login, Navigate to User infor -> change password

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/5560aa4d-c962-4bf0-b288-d6349a7158ec)

#### To create an application in ArgoCD

Just click - Create an Application

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/854bda20-8d6a-41b7-8d9e-38722984dbe4)

    Application name - myapp
    Project name - default
    SYNC POLICY - Automatic

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/04b7b101-8721-43ed-9a18-3714551fbd11)

You can use below GitHub repo url for ArgoCD application Repository URL

    https://github.com/argoproj/argocd-example-apps

    Revision - HEAD
    Path - guestbook (You can see this project in above GitHub URL)
    Destination - Cluster URL - https://kubernetes.default.svc (Popping up automatically)
    Namespace - default
    others leave as default

Finally select create option to create application.

Once created, the ArgoCD will pull the deployment and service yaml files of guestbook from Github and deploy to Kubenetes Cluster.

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/216c529e-3505-4835-bcdb-7514272ac1eb)

If you check with Kubernetes cluster using below command

    kubectl get deploy

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/23fafa63-5c3a-4e66-b09f-e5e2248c475b)

If I click myapp in ArgoCD, then it will give overview of deployment like status of the deployment, service and pods

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/85c1f7cb-1ace-4e72-9e68-fe5263399168)

##### ArgoCD is un-opinionated - Because ArgoCD wont recommend to use specific format. You can use any format like helm format, json format, ksonnet format or kustomize format.

#### To access the guestbook-ui application in browser

Bydefault, this app is running with port:80.

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/f3bfe3fb-d5b0-4d34-b3fd-6244b2b4f4e8)

So we can port-forward with some other port to avoid conflicting. Just use below command to access the app with port:9090

    kubectl port-forward svc/guestbook-ui 9090:80 --address='0.0.0.0'
    or
    nohup kubectl port-forward svc/guestbook-ui 9090:80 --address='0.0.0.0' &

Now, I can access the guestbook-ui app through browser with cluster-ip:9090

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/846fb0a5-e755-4d40-9884-9ec88d742039)

This is the way to deploy the app to kubernetes cluster using ArgoCD manifest file method. 

Now Im going to deploy the app to kubernetes cluster using ArgoCD with help template. In order to work, I just delete the application in ArgoCD portal.

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/3259ee7d-7165-4c18-913e-967a42c792cc)

After deletion, there is no running pods

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/7c13e448-d677-4cfe-b7bc-72f6af5143f4)

#### To deploy the app on K8 cluster with the help of helm template

Navigate to ArgoCD portal and create a new application again.

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/6cec62d5-9607-4a55-aa6d-cba27f2041f7)

Repository URL is same as we have used before

    https://github.com/argoproj/argocd-example-apps

This time I good to go with helm-guestbook.

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/fddad233-1069-465f-a91b-7e2f6c5c95e0)

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/4d61ec6b-fb78-4d37-be67-5c7f45867767)

Then create a application and check the status

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/901580db-e451-470b-bae5-2c00714df55c)

Now check the K8 pods - Yup Its up and running

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/2ad05746-386d-4a71-893e-34d4be7a67d5)

Its too running with port 80. So again im go to port-forward with some other port:9080.

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/ae45b1fe-cbae-4c22-912a-7e3ca7a08287)

    kubectl port-forward svc/myhelmapp-helm-guestbook 9080:80 --address='0.0.0.0'

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/0ffefbe9-2f64-4618-8c3c-6703637258a0)

Perfect! My app is running with port:9080. 

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/dd054b81-ec46-4e25-9935-cc56122f1b46)

Now we have used helm template to deploy app on K8 cluster

Then delete the application in ArgoCD portal.

### How to install ArgoCD CLI in ubuntu machine

SSH to machine and install the commands below,

        wget https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
        chmod +x argocd-linux-amd64
        sudo mv argocd-linux-amd64 /usr/local/bin/argocd
        argocd --help

#### To login ArgoCD

    argocd login 13.201.48.19:8080
    username: admin
    password: ******

![image](https://github.com/kohlidevops/GitOps-ArgoCD/assets/100069489/f4737782-3c73-497b-9818-f178cefbbbc4)

#### To create a app using argocd cli

    argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-namespace default --dest-server https://kubernetes.default.svc     --directory-recurse

#### To list the application

    kubectl get application -n argocd

#### To get the application deployed

    kubectl get deploy -n default


    

