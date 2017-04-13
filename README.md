# Kubernetes Workshop

## Install kubectl
https://kubernetes.io/docs/tasks/kubectl/install/

## Install minikube
https://github.com/kubernetes/minikube/releases/tag/v0.17.1

## Start a cluster
```
minikube stop 
minikube delete
minikube start
```

## Check that kubectl is working
```
kubectl version
```


## 1 start simple

Registries: hub.docker.com

```
kubectl run -it debug --image=pdressel/devcon:debug /bin/bash
env
TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
curl -sSk -H "Authorization: Bearer $TOKEN" https://$KUBERNETES_SERVICE_HOST:$KUBERNETES_PORT_443_TCP_PORT/api/v1 | jq .
kubectl get pods
kubectl exec -it debug-<id> /bin/bash
kubectl scale deployment debug --replicas=3
kubectl get pods
kubectl kubectl scale deployment debug --replicas=1
```

```
minikube ssh
docker ps
docker inspect
```

## namespaces
kubectl get pods
vs
kubectl get pods --namespace=kube-system

