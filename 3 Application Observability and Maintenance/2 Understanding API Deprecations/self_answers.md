Task 1:
```bash
# 1:
# Access the yaml file at /etc/kubernetes/manifests/kube-apiserver.yaml

# 2:
kubectl describe pod kube-apiserver -n kube-system

# 3:
# Edit the yaml file at /etc/kubernetes/manifests/kube-apiserver.yaml

# 4:
kubectl exec -it kube-apiserver-minikube -n kube-system -- kube-apiserver -h | grep enable-admission-plugins
```

Task 2:
```bash
# 1:
kubectl version -o yaml

# 2:
kubectl api-resources --sort-by=kind

# 3:
kubectl explain ingress

# 4:
kubectl api-versions --api-group=autoscaling

# 5:
## Method 1
kubectl proxy 8001
curl localhost:8001/apis/autoscaling | jq .preferredVersion

## Method 2
kubectl get --raw /apis/autoscaling | jq .preferredVersion

# 6:
# Check the yaml file at /etc/kubernetes/manifests/kube-apiserver.yaml
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep runtime-config
```