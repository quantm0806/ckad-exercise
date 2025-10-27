## Services and Networking
### Create a pod with image nginx called nginx and expose its port 80
```bash
kubectl run nginx --image=nginx --port=80 --dry-run=client -o yaml > nginx.yaml
kubectl expose po/nginx --port=80 --target-port=80 --name=nginx-svc
```

### Confirm that ClusterIP has been created. Also check endpoints
```bash
kubectl get svc
kubectl get endpoints
```

### Get service's ClusterIP, create a temp busybox pod and 'hit' that IP with wget
```bash
kubectl get svc
kubectl run busybox --image=busybox --rm -it -- wget <cluster_ip>
```

### Convert the ClusterIP to NodePort for the same service and find the NodePort port. Hit service using Node's IP. Delete the service and the pod at the end.
```bash
kubectl edit svc nginx-svc
kubectl get svc
kubectl cluster-info
curl <node_ip>:<node_port>
kubectl delete svc nginx-svc
kubectl delete po nginx
```

### Create a deployment called foo using image 'dgkanatsios/simpleapp' (a simple server that returns hostname) and 3 replicas. Label it as 'app=foo'. Declare that containers in this pod will accept traffic on port 8080 (do NOT create a service yet)
```bash
kubectl create deploy foo --image=dgkanatsios/simpleapp --replicas=3 --port=8080
kubectl label deploy foo app=foo
```

### Get the pod IPs. Create a temp busybox pod and try hitting them on port 8080
```bash
kubectl get po -o wide
kubectl run temp --image=busybox --rm -it -- wget <pod_ip>:8080
```

### Create a service that exposes the deployment on port 6262. Verify its existence, check the endpoints
```bash
kubectl expose deploy foo --port=6262 --target-port=8080
kubectl get ep
```

### Create a temp busybox pod and connect via wget to foo service. Verify that each time there's a different hostname returned. Delete deployment and services to cleanup the cluster
```bash
kubectl run temp --image=busybox --rm -it -- /bin/sh
wget -qO- <cluster_ip>:6262
```

### Create an nginx deployment of 2 replicas, expose it via a ClusterIP service on port 80. Create a NetworkPolicy so that only pods with labels 'access: granted' can access the pods in this deployment and apply it
kubernetes.io > Documentation > Concepts > Services, Load Balancing, and Networking > [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

Note that network policies may not be enforced by default, depending on your k8s implementation. E.g. Azure AKS by default won't have policy enforcement, the cluster must be created with an explicit support for netpol https://docs.microsoft.com/en-us/azure/aks/use-network-policies#overview-of-network-policy
```bash
kubectl create deploy nginx --replicas=2 --image=nginx
kubectl expose deploy nginx --port=80 --target-port=80 --name=nginx-svc
kubectl apply -f netpol.yaml
```

Create this netpol
```bash
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: nginx-netpol
spec:
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          access: granted
```