Task 1:
```bash
# 1
kubectl create deployment nginx-deploy --image=nginx:stable-alpine --replicas=4

# 2
kubectl expose deploy nginx-deploy --port=9000 --target-port=80 --name=nginx-svc --type=NodePort

# 3
kubectl edit deploy nginx-deploy
# Then find replicas and change it to 6

# 4
# Because we are using minikube but not inside the cluster, we need to get minikube IP
minikube ip
# Then find node port
kubectl get svc
# Then curl it, in my case it is 192.168.49.2:31769
curl 192.168.49.2:31769
```

Task 2:
```bash
# 1: Just edit the file using your IDE or vi, nano,...
# 2:
kubectl apply -f blue.deploy.yml -f blue.svc.yml -f green.deploy.yml -f green.svc.yml -f public.svc.yml

# 3: There are 2 ways to do it
# 3.1: Edit the public svc
kubectl edit svc public.svc.yml
# Then find role and change it to green

# 3.2: Set the selector to green
kubectl set selector svc public-service 'role=green'
```

Task 3:
```bash
# 1
kubectl apply -f canary.deploy.yml -f public.svc.yml -f stable.deploy.yml
kubectl get pods

# 2
kubectl scale --replicas=6 deployment.apps/stable-deployment
kubectl scale --replicas=2 deployment.apps/canary-deployment
kubectl get pods

# 3
kubectl edit deploy canary-deployment
# Then find labels and add app: customer-app

# 4
# Get service cluster IP
kubectl get svc public-service
kubectl run temp-pod --image=alpine --restart=Never -it
apk add curl
curl <service-cluster-ip>
```