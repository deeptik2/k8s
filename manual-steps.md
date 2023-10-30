# Manual steps for k8s

1. Create Deployment 
Ref: https://kubernetes.io/docs/tutorials/hello-minikube/#create-a-deployment
```
kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080

kubectl get deployments
kubectl get pods
```

2. View Cluster Events
```
kubectl get events
```

3. Create Service
https://kubernetes.io/docs/tutorials/hello-minikube/#create-a-service
```
kubectl expose deployment hello-node --type=LoadBalancer --port=8080
```

4. Ingress: https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-ingress-guide-nginx-example.html
