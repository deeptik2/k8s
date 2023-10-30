# Kind create cluster
https://kind.sigs.k8s.io/
1. kind create cluster --name k8s-playground --config cluster-local.yaml
```
kl config get-context
kl config use-context k8s-playground

kl get pods
kl get deployments
kl get services
```

2. kind delete cluster --name k8s-playground

======================
# create namespace
kl create -f chaos-namespace.yaml


===================
# create nginx app in ns=chaos-mesh
* kl apply -f nginx-deployment.yaml --namespace=chaos-mesh
* kl get deployments --namespace=chaos-mesh
kubectl get pods -l app=my-nginx -o wide

kl apply -f nginx-service.yaml
kl get svc my-nginx

===============
# chaos-mesh
2. Install chaos using helm: https://chaos-mesh.org/docs/production-installation-using-helm/ (docker)
```
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm install chaos-mesh chaos-mesh/chaos-mesh -n=chaos-mesh --version 2.6.2 --set controllerManager.replicaCount=1
```
3. Port fwd Chaos Dashboard
```
kl port-forward chaos-dashboard-5db8dd5f99-c28ff 2333:2333 &
```
the dashboard should be available at: http://localhost:2333

4. RBAC
```
kl apply -f chaos-rbac.yaml
kubectl create token account-chaos-mesh-manager-ektmb

or 

kubectl describe -n chaos-mesh secrets account-chaos-mesh-manager-ektmb
```



# config experiment
1. Pod Kill
kl get pods
kl apply -f chaos-exp2-pod-kill.yaml
kl get pods

2. Network delay
*  kl apply -f chaos-exp1.yaml --namespace=chaos-mesh
* kubectl describe networkchaos --namespace=chaos-mesh
```
Error: 
Message:        failed to apply for pod chaos-mesh/nginx-deployment-cbdccf466-fgtbs: unable to flush ip sets for pod nginx-deployment-cbdccf466-fgtbs
        Operation:      Apply
        Timestamp:      2023-10-13T14:29:06Z
        Type:           Failed
```
* debugging `unable to flush ip sets for pod` error
1. NODE_NAME=$(kubectl get pod -n chaos-mesh my-nginx-5fc5bcc97d-ml8xw -o jsonpath='{.spec.nodeName}')
2. CHAOS_DAEMON_POD=$(kubectl get pod -n chaos-mesh --field-selector=spec.nodeName=k8s-playground-worker2 -l app.kubernetes.io/component=chaos-daemon -o jsonpath='{.items[0].metadata.name}')
3. kubectl logs -n chaos-testing chaos-daemon-dvwgq





## Links
https://spacelift.io/blog/kubectl-apply-vs-create
https://home.robusta.dev/blog/kubernetes-service-vs-loadbalancer-vs-ingress#:~:text=Ingresses%20are%20used%20together%20with,which%20points%20to%20the%20Pod.