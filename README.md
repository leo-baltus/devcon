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
```
kubectl get pods
vs
kubectl get pods --namespace=kube-system
```


## Serviceaccount
'default' is in elke namespace aanwezig
```
kubectl get serviceAccounts

kubectl get pods/<podname> -o yaml  | grep serviceAccountName
```
De 'default' kan standaard bij de api:
```
cat /var/run/secrets/kubernetes.io/serviceaccount/token
```

## dashboard
```
minikube dashboard
```
maar zie ook cluster-info
```
kubectl cluster-info

```

##opruimen
```
kubectl get deploy
kubectl delete  deploy debug
kubectl get pods
<leeg>
```

## 2 Dockerfile
```
cat debug/Dockerfile
cat frontend/Dockerfile
```

## 3 frontend
hoe vind het front-end de backend:
```
grep backend frontend/frontend-deployment.yaml
```

start het frontend
kubectl create -f frontend/frontend-deployment.yaml

```
kubectl get deployments
kubectl get pods
kubectl logs <pod name>
kubectl port-forward <pod name> 8080
open http://localhost:8080/
```
Failed to reach the backend 😐

## 4 backend

```
kubectl create -f backend/backend-deployment.yaml
kubectl get deployments
kubectl get pods
kubectl logs <pod name>
kubectl port-forward <pod name> 8080
open http://localhost:8080/
```

Failed to reach the backend 😐
(nog geen service)

## 5  frontend & backend service
```
kubectl create -f frontend/frontend-service.yaml
kubectl get services
kubectl create -f backend/backend-service.yaml
kubectl get services
```

Response from the backend: "Failed to reach mongodb 😐"

## 6 access frontend from outside the cluster
```
kubectl describe service frontend
open http://$(minikube ip):<nodeport>
```

Response from the backend: "Failed to reach mongodb 😐"

## 7 mongodb StatefulSet. (v/h PetSet)

```
kubectl create -f mongodb/mongodb-service.yaml
kubectl create -f mongodb/mongodb-statefulset.yaml
kubectl get services
kubectl get statefulsets
kubectl get pods
kubectl get persistentvolumeclaims
open http://$(minikube ip):<nodeport>
```

## 8 Scale all the things!
```
kubectl scale deployment frontend --replicas=5
kubectl scale deployment backend --replicas=3
kubectl get pods
```


'Pending state' ?

```
kubectl get events
```

scale down if needed
```
kubectl scale deployment frontend --replicas=2
```


## 9 Do a almost-rolling update of the frontend

```
kubectl apply -f frontend/frontend-deployment2.yaml
```

## 10 ingress controller

```
kubectl create -f controller.yaml
kubectl create -f frontend/frontend-ingress.yaml
kubectl get ingresses
<edit /etc/hosts to resolve frontend.workshop to minikube ip>
<edit /etc/hosts to resolve fake.workshop to minikube ip>
kubectl describe service nginx-ingress-lb
open http://frontend.workshop:<nginx-ingress-lb nodeport>
vs
open http://fake.workshop:<nginx-ingress-lb nodeport>
```
