## ConfigMaps
kubernetes.io > Documentation > Tasks > Configure Pods and Containers > [Configure a Pod to Use a ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)

### Create a configmap named config with values foo=lala,foo2=lolo
```bash
kubectl create cm config --from-literal=foo=lala --from-literal=foo2=lolo
```

### Display its values
```bash
kubectl describe cm config
```

### Create and display a configmap from a file
Create the file with
```bash
echo -e "foo3=lili\nfoo4=lele" > config.txt
```

```bash
kubectl create cm config-from-file --from-file=config.txt
kubectl describe cm config-from-file
```

### Create and display a configmap from a .env file
Create the file with the command

```bash
echo -e "var1=val1\n# this is a comment\n\nvar2=val2\n#anothercomment" > config.env
```

```bash
kubectl create cm config-from-env-file --from-file=config.txt
kubectl describe cm config-from-env-file

```

### Create and display a configmap from a file, giving the key 'special'
Create the file with

```bash
echo -e "var3=val3\nvar4=val4" > config4.txt
```
```bash
kubectl create cm config-from-file-with-key --from-file=special=config4.txt
kubectl describe cm config-from-file-with-key
```

### Create a configMap called 'options' with the value var5=val5. Create a new nginx pod that loads the value from variable 'var5' in an env variable called 'option'
```bash
kubectl create cm options --from-literal=var5=val5
kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > nginx.yaml
# Edit the yaml file to add the configMap as an env variable
vim nginx.yaml
kubectl apply -f nginx.yaml
```

The nginx.yaml will look like this
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
    env:
    - name: option
      valueFrom:
        configMapKeyRef:
          name: options
          key: var5
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  volumes:
  - name: config
    configMap:
      name: options
status: {}
```

### Create a configMap 'anotherone' with values 'var6=val6', 'var7=val7'. Load this configMap as env variables into a new nginx pod
```bash
kubectl create cm anotherone --from-literal=var6=val6 --from-literal=var7=val7
kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > nginx.yaml
# Edit the yaml file to add the configMap as an env variable
vim nginx.yaml
kubectl apply -f nginx.yaml
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
    envFrom:
    - configMapRef:
        name: anotherone
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  volumes:
  - name: anotherone
    configMap:
      name: anotherone
status: {}
```

### Create a configMap 'cmvolume' with values 'var8=val8', 'var9=val9'. Load this as a volume inside an nginx pod on path '/etc/lala'. Create the pod and 'ls' into the '/etc/lala' directory.
```bash
kubectl create cm cmvolume --from-literal=var8=val8 --from-literal=var9=val9
kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > nginx.yaml
# Edit the yaml file to add the configMap as an env variable
vim nginx.yaml
kubectl apply -f nginx.yaml
kubectl exec -it nginx -- ls /etc/lala
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
    volumeMounts:
    - name: cmvolume
      mountPath: "/etc/lala"
      readOnly: true
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  volumes:
  - name: cmvolume
    configMap:
      name: cmvolume
status: {}
```

## SecurityContext
kubernetes.io > Documentation > Tasks > Configure Pods and Containers > [Configure a Security Context for a Pod or Container](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

### Create the YAML for an nginx pod that runs with the user ID 101. No need to create the pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  securityContext:
    runAsUser: 101
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

### Create the YAML for an nginx pod that has the capabilities "NET_ADMIN", "SYS_TIME" added to its single container
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
    securityContext:
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

## Resource requests and limits
kubernetes.io > Documentation > Tasks > Configure Pods and Containers > [Assign CPU Resources to Containers and Pods](https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/)

### Create an nginx pod with requests cpu=100m,memory=256Mi and limits cpu=200m,memory=512Mi
```bash
kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > nginx.yaml
# Edit the file
vim nginx.yaml
```

The `nginx.yaml` will look like:
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
    resources: 
      requests:
        cpu: 100m
        memory: 256Mi
      limits:
        cpu: 200m
        memory: 512Mi
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

Limit Ranges
kubernetes.io > Documentation > Concepts > Policies > Limit Ranges (https://kubernetes.io/docs/concepts/policy/limit-range/)

### Create a namespace named limitrange with a LimitRange that limits pod memory to a max of 500Mi and min of 100Mi
```bash
```
### Describe the namespace limitrange
```bash
```
### Create an nginx pod that requests 250Mi of memory in the limitrange namespace
```bash
```
Resource Quotas
kubernetes.io > Documentation > Concepts > Policies > Resource Quotas (https://kubernetes.io/docs/concepts/policy/resource-quotas/)

### Create ResourceQuota in namespace one with hard requests cpu=1, memory=1Gi and hard limits cpu=2, memory=2Gi.
```bash
```
### Attempt to create a pod with resource requests cpu=2, memory=3Gi and limits cpu=3, memory=4Gi in namespace one
```bash
```
### Create a pod with resource requests cpu=0.5, memory=1Gi and limits cpu=1, memory=2Gi in namespace one
```bash
```
Secrets
kubernetes.io > Documentation > Concepts > Configuration > Secrets

kubernetes.io > Documentation > Tasks > Inject Data Into Applications > Distribute Credentials Securely Using Secrets

### Create a secret called mysecret with the values password=mypass
```bash
```
Create a secret called mysecret2 that gets key/value from a file
Create a file called username with the value admin:

### echo -n admin > username
```bash
```
### Get the value of mysecret2
```bash
```
### Create an nginx pod that mounts the secret mysecret2 in a volume on path /etc/foo
```bash
```
### Delete the pod you just created and mount the variable 'username' from secret mysecret2 onto a new nginx pod in env variable called 'USERNAME'
```bash
```
### Create a Secret named 'ext-service-secret' in the namespace 'secret-ops'. Then, provide the key-value pair API_KEY=LmLHbYhsgWZwNifiqaRorH8T as literal.
```bash
```
### Consuming the Secret. Create a Pod named 'consumer' with the image 'nginx' in the namespace 'secret-ops' and consume the Secret as an environment variable. Then, open an interactive shell to the Pod, and print all environment variables.
```bash
```
### Create a Secret named 'my-secret' of type 'kubernetes.io/ssh-auth' in the namespace 'secret-ops'. Define a single key named 'ssh-privatekey', and point it to the file 'id_rsa' in this directory.
```bash
```
### Create a Pod named 'consumer' with the image 'nginx' in the namespace 'secret-ops', and consume the Secret as Volume. Mount the Secret as Volume to the path /var/app with read-only access. Open an interactive shell to the Pod, and render the contents of the file.
```bash
```
ServiceAccounts
kubernetes.io > Documentation > Tasks > Configure Pods and Containers > Configure Service Accounts for Pods

### See all the service accounts of the cluster in all namespaces
```bash
```
### Create a new serviceaccount called 'myuser'
```bash
```
### Create an nginx pod that uses 'myuser' as a service account
```bash
```
### Generate an API token for the service account 'myuser'
```bash
```