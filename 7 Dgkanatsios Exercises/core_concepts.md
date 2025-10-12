Create a namespace called 'mynamespace' and a pod with image nginx called nginx on this namespace
```bash
kubectl create ns mynamespace
kubectl run nginx --image=nginx -n mynamespace
```

Create the pod that was just described using YAML
```bash
kubectl run nginx --image=nginx -n mynamespace -o yaml --dry-run=client > my_nginx.yml
kubectl apply -f my_nginx.yml
```

Create a busybox pod (using kubectl command) that runs the command "env". Run it and see the output
```bash
kubectl run busybox --image=busybox --restart=Never -- env
kubectl logs busybox
```

Create a busybox pod (using YAML) that runs the command "env". Run it and see the output
```bash
kubectl run busybox --image=busybox --restart=Never -o yaml --dry-run=client -- env > my_busybox.yml
kubectl apply -f my_busybox.yml
```

Get the YAML for a new namespace called 'myns' without creating it
```bash
kubectl create ns myns -o yaml --dry-run=client > myns.yml
kubectl apply -f myns.yml
```

Create the YAML for a new ResourceQuota called 'myrq' with hard limits of 1 CPU, 1G memory and 2 pods without creating it
```bash
kubectl create quota quota_myrq -n myrq --hard=cpu=1,memory=1G,pods=2 --dry-run=client -o yaml > quota_myrq.yml
kubectl apply -f quota_myrq.yml
```

Get pods on all namespaces
```bash
kubectl get po -A
```

Create a pod with image nginx called nginx and expose traffic on port 80
```bash
kubectl run nginx --image=nginx --port=80
```

Change pod's image to nginx:1.24.0. Observe that the container will be restarted as soon as the image gets pulled
```bash
kubectl set image po/nginx nginx=nginx:1.24.0
```

Get nginx pod's ip created in previous step, use a temp busybox image to wget its '/'
```bash
kubectl get po nginx -o wide
kubectl run busybox --image=busybox --restart=Never -- wget <NGINX IP>
```

Get pod's YAML
```bash
kubectl get po nginx -o yaml
```

Get information about the pod, including details about potential issues (e.g. pod hasn't started)
```bash
kubectl describe po nginx
```

Get pod logs
```bash
kubectl logs nginx 
```

If pod crashed and restarted, get logs about the previous instance
```bash
kubectl logs nginx -p
```

Execute a simple shell on the nginx pod
```bash
kubectl exec -it nginx -- bash
```

Create a busybox pod that echoes 'hello world' and then exits
```bash
kubectl run busybox --image=busybox --command -- echo "hello world"
```

Do the same, but have the pod deleted automatically when it's completed
```bash
kubectl run busybox --image=busybox --restart=Never -it --rm -- echo "hello world"
```

Create an nginx pod and set an env value as 'var1=val1'. Check the env value existence within the pod
```bash
kubectl run nginx --image=nginx -it --restart=Never --rm --env=var1=val1 --env=var2=val2 -- env
 ```