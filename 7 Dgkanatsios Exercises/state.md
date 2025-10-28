kubernetes.io > Documentation > Tasks > Configure Pods and Containers > [Configure a Pod to Use a Volume for Storage](https://kubernetes.io/docs/tasks/configure-pod-container/configure-volume-storage/)

kubernetes.io > Documentation > Tasks > Configure Pods and Containers > [Configure a Pod to Use a PersistentVolume for Storage](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/)


## Define volumes
### Create busybox pod with two containers, each one will have the image busybox and will run the 'sleep 3600' command. Make both containers mount an emptyDir at '/etc/foo'. Connect to the second busybox, write the first column of '/etc/passwd' file to '/etc/foo/passwd'. Connect to the first busybox and write '/etc/foo/passwd' file to standard output. Delete pod.
```bash
kubectl run busybox --image=busybox --restart=Never $do --command -- bin/sh -c "sleep 3600" > busybox.yaml
# Edit busybox.yaml, the yaml will look like yaml below
kubectl apply -f busybox.yaml
kubectl exec busybox -c busybox2 -it -- /bin/sh
# Edit file etc/foo/passwd
kubectl exec busybox -c busybox -- cat /etc/foo/passwd
kubectl delete -f busybox.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - command:
    - bin/sh
    - -c
    - sleep 3600
    image: busybox
    name: busybox
    resources: {}
    volumeMounts:
    - mountPath: /etc/foo
      name: vol
  - command:
    - bin/sh
    - -c
    - sleep 3600
    image: busybox
    name: busybox2
    resources: {}
    volumeMounts:
    - mountPath: /etc/foo
      name: vol
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  volumes:
  - name: vol
    emptyDir: {}
status: {}
```
### Create a PersistentVolume of 10Gi, called 'myvolume'. Make it have accessMode of 'ReadWriteOnce' and 'ReadWriteMany', storageClassName 'normal', mounted on hostPath '/etc/foo'. Save it on pv.yaml, add it to the cluster. Show the PersistentVolumes that exist on the cluster
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: myvolume
spec:
  storageClassName: normal
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
    - ReadWriteMany
  hostPath:
    path: "/etc/foo"
```

```bash
kubectl apply -f pv.yaml
kubectl get pv
```

### Create a PersistentVolumeClaim for this PersistentVolume, called 'mypvc', a request of 4Gi and an accessMode of ReadWriteOnce, with the storageClassName of normal, and save it on pvc.yaml. Create it on the cluster. Show the PersistentVolumeClaims of the cluster. Show the PersistentVolumes of the cluster
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  storageClassName: normal
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 4Gi
```

```bash
kubectl apply -f pvc.yaml
kubectl get pvc
kubectl get pv
```

### Create a busybox pod with command 'sleep 3600', save it on pod.yaml. Mount the PersistentVolumeClaim to '/etc/foo'. Connect to the 'busybox' pod, and copy the '/etc/passwd' file to '/etc/foo/passwd'
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - command:
    - /bin/sh
    - -c
    - sleep 3600
    image: busybox
    name: busybox
    resources: {}
    volumeMounts:
    - name: pvc
      mountPath: "/etc/foo"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
  - name: pvc
    persistentVolumeClaim:
      claimName: mypvc
status: {}
```

```bash
kubectl apply -f pod.yaml
kubectl exec -it busybox -- cp /etc/passwd etc/foo/passwd 
 ```

### Create a second pod which is identical with the one you just created (you can easily do it by changing the 'name' property on pod.yaml). Connect to it and verify that '/etc/foo' contains the 'passwd' file. Delete pods to cleanup. Note: If you can't see the file from the second pod, can you figure out why? What would you do to fix that?
```bash
cp pod.yaml pod2.yaml
# Rename pod name in pod2.yaml from busybox to busybox2
kubectl exec -it busybox2 -- ls /etc/foo 
```

### Create a busybox pod with 'sleep 3600' as arguments. Copy '/etc/passwd' from the pod to your local folder
```bash
kubectl run busybox --image=busybox -- /bin/sh -c "sleep 3600"
kubectl cp busybox:etc/passwd ./passwd
cat passwd
```