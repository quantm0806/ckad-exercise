Task 1:
```bash
# 1:
# Remove ``` from the dockerfile, set version 7.0 for image inside dockerfile
docker build -t health-probes -f health-probes/dockerfile ./health-probes

# 2:
## Run this command to have a quick template
kubectl create deploy health-probes-deployment --image=health-probes --dry-run=client -o yaml > health-probes-deployment.yml

# 3:
kubectl apply -f health-probes-deployment.yml

# 4:
kubectl describe pod health-probes-deployment-<pod-hash>

# 5:
kubectl get pods
```

Task 2:
```bash
# 1:
kubectl run busybox-probes --image=k8s.gcr.io/busybox -o yaml --dry-run="client" > busy-box.pod.yml
# Edit liveness and readiness probe

# 2:
kubectl apply -f busy-box.pod.yml

# 3:
kubectl get pods
kubectl describe pod

# 4:
kubectl get pods --watch
```