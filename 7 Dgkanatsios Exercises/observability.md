## Liveness, readiness and startup probes
kubernetes.io > Documentation > Tasks > Configure Pods and Containers > [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

### Create an nginx pod with a liveness probe that just runs the command 'ls'. Save its YAML in pod.yaml. Run it, check its probe status, delete it.
```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
# Edit pod.yaml as follow and continue to run
kubectl apply -f pod.yaml
kubectl describe po nginx | grep -i liveness
kubectl delete -f pod.yaml
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
    resources: {}
    livenessProbe:
      exec:
        command:
        - ls
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

### Modify the pod.yaml file so that liveness probe starts kicking in after 5 seconds whereas the interval between probes would be 5 seconds. Run it, check the probe, delete it.
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
    resources: {}
    livenessProbe:
      exec:
        command:
        - ls
      initialDelaySeconds: 5
      periodSeconds: 5
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

### Create an nginx pod (that includes port 80) with an HTTP readinessProbe on path '/' on port 80. Again, run it, check the readinessProbe, delete it.
```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
# Edit pod.yaml as follow and continue to run
kubectl apply -f pod.yaml
kubectl describe po nginx | grep -i readiness
kubectl delete -f pod.yaml
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
    resources: {}
    ports:
    - containerPort: 80
    readinessProbe:
      httpGet:
        path: /
        port: 80
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

### Lots of pods are running in qa,alan,test,production namespaces. All of these pods are configured with liveness probe. Please list all pods whose liveness probe are failed in the format of <namespace>/<pod name> per line.
```bash
kubectl get po -o jsonpath="{range .items[?(..restartCount != 0)]}[{.metadata.name}, {.metadata.namespace}]{'\n'}{end}" -A
```

## Logging
### Create a busybox pod that runs i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done. Check its logs
```bash
kubectl run busybox --image=busybox --command -- /bin/sh -c "i=0; while true; do echo \"$i: \$(date)\"; i=$((i+1)); sleep 1; done"
kubectl logs busybox
```

## Debugging
### Create a busybox pod that runs 'ls /notexist'. Determine if there's an error (of course there is), see it. In the end, delete the pod
```bash
kubectl run busybox --image=busybox -- ls /notexist
kubectl get po busybox
kubectl delete po busybox
```

### Create a busybox pod that runs 'notexist'. Determine if there's an error (of course there is), see it. In the end, delete the pod forcefully with a 0 grace period
```bash
kubectl run busybox --image=busybox -- notexist
kubectl logs busybox
kubectl delete po busybox --force --grace-period=0
```
### Get CPU/memory utilization for nodes (metrics-server must be running)
```bash
kubectl top node
```
