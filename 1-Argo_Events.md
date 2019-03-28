

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

40 mins

## STEP 1: Installing Argo Events

1. login to system.

2. Add `argoproj` repository
```bash
helm repo add argo https://argoproj.github.io/argo-helm
```
3. Install `argo-events` chart

```bash
helm install argo/argo-events
```

4. Using Kubectl
Run the following commands:
```yaml
kubectl create namespace argo-events
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/argo-events-sa.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/argo-events-cluster-roles.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/sensor-crd.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/gateway-crd.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/sensor-controller-configmap.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/sensor-controller-deployment.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/gateway-controller-configmap.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/gateway-controller-deployment.yaml
```

## STEP 2: Get Started
1. To deploy a webhook first, we need to setup event sources
```bash
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/examples/gateways/webhook-gateway-configmap.yaml 
```

2. To create webhook gateway:
```bash
 kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/examples/gateways/webhook-http.yaml
```

3. To create webhook sensor:
```bash
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/examples/sensors/webhook-http.yaml
```

4. After running them, we trigger the webhook by an HTTP POST request
```json
export WEBHOOK_SERVICE_URL=$(minikube service -n argo-events --url <gateway_service_name>)
 echo $WEBHOOK_SERVICE_URL
 curl -d '{"message":"this is my first webhook"}' -H "Content-Type: application/json" -X POST $WEBHOOK_SERVICE_URL/foo
```

