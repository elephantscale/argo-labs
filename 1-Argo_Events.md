

# Lab  : Argo

### Overview

We will be installing requirements and running event-based activities

### Requirements
* Installed helm client. to install : 
```bash
https://docs.helm.sh/using_helm/
```

* Running Tiler server
* Builds on previous lab: Argo_Installation.md

### Run time

30 mins

## STEP 0: Installing Argo

1. login to system.

2. If you do not already have the file, download it here:

```bash
curl -sSL -o /usr/local/bin/argo https://github.com/argoproj/argo/releases/download/v2.2.1/argo-linux-amd64
```
3. Set the permission
```bash
chmod +x /usr/local/bin/argo
```
4. Install the Controller and UI
```bash
kubectl create ns argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/v2.2.1/manifests/install.yaml
```

## STEP 1: Configure the service account to run workflows

1. Run the following command to grant admin privileges to the 'default' service account in the namespace 'default
```bash
kubectl create rolebinding default-admin --clusterrole=admin --serviceaccount=default:default
```

## STEP 2: Run Simple Example Workflows
1. Run the following argo commands:
```bash
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/coinflip.yaml
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/loops-maps.yaml
argo list
argo get xxx-workflow-name-xxx
argo logs xxx-pod-name-xxx #from get command above
```

2. Creating workflows directly with kubectl(offers less features)
```bash
kubectl create -f https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml
kubectl get wf
kubectl get wf hello-world-xxx
kubectl get po --selector=workflows.argoproj.io/workflow=hello-world-xxx --show-all
kubectl logs hello-world-yyy -c main
```