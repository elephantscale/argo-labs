# Lab  : Argo Workflow Templates by Example

### Overview

We will be learning most useful commands and creating a very simple workflow template.

### Builds on 
* 1- Argo_Installation.md

### Run time

30 mins

## STEP 0: Commands

1. Using Argo CLI:

```bash
argo submit hello-world.yaml    # submit a workflow spec to Kubernetes
argo list                       # list current workflows
argo get hello-world-xxx        # get info about a specific workflow
argo logs -w hello-world-xxx    # get logs from all steps in a workflow
argo logs hello-world-xxx-yyy   # get logs from a specific step in a workflow
argo delete hello-world-xxx     # delete workflow
```

2. Using kubectl:

```bash
kubectl create -f hello-world.yaml
kubectl get wf
kubectl get wf hello-world-xxx
kubectl get po --selector=workflows.argoproj.io/workflow=hello-world-xxx --show-all  # similar to argo
kubectl logs hello-world-xxx-yyy -c main
kubectl delete wf hello-world-xxx
```





## STEP 1: Creating a simple workflow ("Hello World)

1. Open the shell 
2. Run the following command:

```bash
docker run docker/whalesay cowsay "hello world"
```

docker/whalesay = a container

3. For running the same container on Kubernetes cluster using an Argo workflow template:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow                  # new type of k8s spec
metadata:
  generateName: hello-world-    # name of the workflow spec
spec:
  entrypoint: whalesay          # invoke the whalesay template
  templates:
  - name: whalesay              # name of the template
    container:
      image: docker/whalesay
      command: [cowsay]
      args: ["hello world"]
      resources:                # limit the resources
        limits:
          memory: 32Mi
          cpu: 100m
```

## STEP 2: Creating more complex workflow spec with parameters

Now we determine an input parameter named `message` which is passed as the `args` to the `cowsy` command.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: hello-world-parameters-
spec:
  # invoke the whalesay template with
  # "hello world" as the argument
  # to the message parameter
  entrypoint: whalesay
  arguments:
    parameters:
    - name: message
      value: hello world

  templates:
  - name: whalesay
    inputs:
      parameters:
      - name: message       # parameter declaration
    container:
      # run cowsay with that message input parameter as args
      image: docker/whalesay
      command: [cowsay]
      args: ["{{inputs.parameters.message}}"]
```

## STEP 3: Override parameters from argo CLI
 The following command binds the `message` parameter to "goodbye world" 
```bash
argo submit arguments-parameters.yaml -p message="goodbye world"
```
