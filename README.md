# autoscaling tester operator

A native kubernetes platform application to deploy concurrent replicas during a specified amount of time by extending the kubernetes api through customer resource definition.

This operator has been developped using the [operator framework](https://operatorframework.io/) using [Ansible](https://www.ansible.com/)

Github page site has been setup in order to serve static files for helm and is available [here](https://learnweb3.github.io/autoscaling-tester-operator/)


## Quick start

```bash
# Using kubectl
# install crds
kubectl apply -f ./k8s/autoscaling-tester-operator/k8s/autoscalingtestjobs.cache.students-epitech.ovh.yaml
# deploy the operator and related kubernetes api's access configurations through service account rbac configurations
kubectl apply -f ./k8s/autoscaling-tester-operator.yaml
# deploy a sample AutoscalingTestJob sending http request to the kubequest application in a loop every 2s with 15 replicas during 5 minutes
kubectl apply -f ./k8s/kubequest.autoscalingtestjob.yaml
```

```bash
# using helm 3
helm repo add autoscaling-tester-operator https://learnweb3.github.io/autoscaling-tester-operator/
# install the chart
helm upgrade --install autoscaling-tester-operator autoscaling-tester-operator/autoscaling-tester-operator  -n kubequest --create-namespace
```

## Values

```yaml
# here are the default values for the operator
image: antoineleguillou/autoscaling-tester-operator:2024-07-09.22
replicas: 1
resources:
  limits:
    cpu: 100m
    memory: 100Mi
  requests:
    cpu: 100m
    memory: 100Mi
```

## CRDs - AutoscalingTestJob

```yaml
# just spamming request to a local service...
apiVersion: cache.students-epitech.ovh/v1alpha1
kind: AutoscalingTestJob
metadata:
  name: kubequest
spec:
  job:
    durationMs: 300000
    replicas: 15
    image: antoineleguillou/alpine-curl:2024-07-12.1
    command: ["sh", "-c"]
    args:
      - while true; do curl --fail -X GET 'http://kubequest.kubequest.svc.cluster.local:80'; sleep 2; done;
    resources:
      requests:
        cpu: 10m
        memory: 50Mi
      limits:
        cpu: 50m
        memory: 100Mi
```

## Package the helm chart

```bash
# create a tar gz file in the helm directory
helm package charts/autoscaling-tester-operator -d docs
# create an index.yaml file to index the helm charts
helm repo index docs --url https://learnweb3.github.io
```
