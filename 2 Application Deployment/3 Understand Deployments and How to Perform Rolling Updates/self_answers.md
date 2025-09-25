Task 1:
```bash
# 1:
kubectl create ns dev

# 2:
kubectl create deploy web-app --image=nginx:1.17.8-alpine --replicas=6 --save-config

# 3:
kubectl get pods

# 4:
kubectl set image deploy/web-app nginx=nginx:1.23.1-alpine

# 5:
kubectl annotate deploy/web-app kubernetes.io/change-cause="Changed to nginx:1.23.1"

# 6:
kubectl rollout history deploy/web-app

# 7:
kubectl describe deploy/web-app

# 8:
kubectl rollout status deploy/web-app
```

Task 2:
```bash
# 1:
kubectl create deploy biz-app --image=nginx:1.17.8-alpine --replicas=4 --dry-run=client -o yaml > biz-app.deploy.yml

# 2:
kubectl create -f biz-app.deploy.yml --save-config

# 3:
kubectl get pods

# 4:
kubectl apply -f biz-app.deploy.yml --record

# 5:
kubectl rollout status deploy/biz-app
kubectl rollout history deploy/biz-app

# 6:
kubectl rollout undo deploy/biz-app
kubectl rollout history deploy/biz-app

# 7:
kubectl get deploy/biz-app -o json | jq .spec.template.spec.containers[0].image
```