Create a Pod with two containers, both with image busybox and command "echo hello; sleep 3600". Connect to the second
container and run 'ls'

```bash
kubectl run multi-busybox --image=busybox --command --dry-run=client -o yaml -- bin/sh -c "echo hello; sleep 3600" > multi_busybox.yaml
# Edit multi_busybox.yaml to have two containers. The yaml should look like the one below
kubectl apply -f multi_busybox.yaml
kubectl exec -it multi-busybox -c multi-busybox-2 -- /bin/sh
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: multi-busybox
  name: multi-busybox
spec:
  containers:
  - command: ["sh", "-c", "echo hello; sleep 3600"]
    image: busybox
    name: multi-busybox-1
    resources: {}
  - command: ["sh", "-c", "echo hello; sleep 3600"]
    image: busybox
    name: multi-busybox-2
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

Create a pod with an nginx container exposed on port 80. Add a busybox init container which downloads a page using '
echo "Test" > /work-dir/index.html'. Make a volume of type emptyDir and mount it in both containers. For the nginx
container, mount it on "/usr/share/nginx/html" and for the initcontainer, mount it on "/work-dir". When done, get the IP
of the created pod and create a busybox pod and run "wget -O- IP"
```bash
# Create multi-nginx-pod.yaml as yaml below
kubectl apply -f multi-nginx-pod.yaml
kubectl get po -o wide
kubectl run busybox --image=busybox -- wget -O- IP
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
    resources: {}
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: cache-volume
  initContainers:
  - image: busybox
    name: busybox
    command: ["sh", "-c", "echo 'Test Hello world' > /work-dir/index.html"]
    volumeMounts:
    - mountPath: /work-dir
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```