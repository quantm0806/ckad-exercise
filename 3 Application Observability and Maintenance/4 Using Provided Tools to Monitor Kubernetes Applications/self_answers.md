Task 1:
```bash
# 1:
minikube addons list
minikube addons enable metrics-server

# 2:
kubectl create deploy nginx --image=nginx --replicas=10

# 3:
kubectl expose deploy/nginx --port=80 --target-port=80 --name=nginx-svc --type=NodePort

# 4:
kubectl get svc
minikube ip
curl <minikube_ip>:<nodeport>

# 5:
kubectl top node
kubectl top pod

# 6:
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes/minikube" | jq '.'
```