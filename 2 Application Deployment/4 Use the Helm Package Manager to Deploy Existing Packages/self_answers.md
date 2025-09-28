Task 1:
```bash
# 1:
kubectl create ns dev
kubectl config set-context --current --namespace=dev

# 2:
helm repo add bitnami https://charts.bitnami.com/bitnami

# 3:
helm repo list

# 4:
helm searh repo nginx --versions

# 5:
helm install nginx-app bitnami/nginx

# 6:
kubectl get pods

# 7:
helm list

# 8:
helm uninstall nginx-app
```

Task 2:
```bash
# 1:
helm pull bitnami/wordpress --version 15.0.9 --untar

# 2:
helm dep list wordpress

# 3:
helm show values wordpress

# 4:
# Nothing

# 5:
helm install -f wordpress-values.yml my-wordpress ./wordpress

# 6:
kubectl get pods
```

Task 3:
```bash
# 1:
helm repo list

# 2:
helm repo update bitnami

# 3:
helm search repo nginx --version 13.1.5

# 4:
helm install --set replicaCount=5 nginx-app bitnami/nginx --version 13.1.5

# 5:
kubectl get pods

# 6:
helm upgrade nginx-app bitnami/nginx --version 13.1.5

# 7:
kubectl get pods

# 8:
helm uninstall nginx-app
```