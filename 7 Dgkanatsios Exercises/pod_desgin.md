## Labels and Annotations

kubernetes.io > Documentation > Concepts > Overview > Working with Kubernetes
Objects > [Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors)

### Create 3 pods with names nginx1,nginx2,nginx3. All of them should have the label app=v1

```bash
kubectl run nginx1 --image=nginx -l app=v1
kubectl run nginx2 --image=nginx -l app=v1
kubectl run nginx3 --image=nginx -l app=v1
```

### Show all labels of the pods

```bash
kubectl get po --show-labels
```

### Change the labels of pod 'nginx2' to be app=v2

```bash
kubectl label pod nginx2 --overwrite app=v2
```

### Get the label 'app' for the pods (show a column with APP labels)

```bash
kubectl get po -L app
```

### Get only the 'app=v2' pods

```bash
kubectl get po -l app=v2
```

### Get 'app=v2' and not 'tier=frontend' pods

```bash
kubectl get po -l app=v2,tier!=frontend
```

### Add a new label tier=web to all pods having 'app=v2' or 'app=v1' labels

```bash
kubectl label pod -l='app in (v1,v2)' tier=web
```

### Add an annotation 'owner: marketing' to all pods having 'app=v2' label

```bash
kubectl annotate -l app=v2 owner=marketing
```

### Remove the 'app' label from the pods we created before

```bash
kubectl label po --all app-
```

### Annotate pods nginx1, nginx2, nginx3 with "description='my description'" value

```bash
kubectl annotate po nginx1 nginx2 nginx3 description='my description'
```

### Check the annotations for pod nginx1

```bash
kubectl annotate po nginx1 --list
```

### Remove the annotations for these three pods

```bash
kubectl annotate po nginx{1..3} description- owner-
```

### Remove these pods to have a clean state in your cluster

```bash
kubectl delete po nginx{1..3}
```

## Pod Placement

### Create a pod that will be deployed to a Node that has the label 'accelerator=nvidia-tesla-p100'

```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yml
# Edit nginx-pod.yml, add accelerator=nvidia-tesla-p100 to the nodeSelector
kubectl apply -f nginx-pod.yml
```

The nginx-pod.yml will look like this

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  nodeSelector:
    accelerator: nvidia-tesla-p100
  containers:
    - image: nginx
      name: nginx
      resources: { }
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: { }
```

### Taint a node with key tier and value frontend with the effect NoSchedule. Then, create a pod that tolerates this taint.

```bash
kubectl taint node minikube tier=front:NoSchedule
kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yml
# Edit nginx-pod.yml, add tier=front to the tolerations
kubectl apply -f nginx-pod.yml
```

The nginx-pod.yml will look like this

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  tolerations:
    - key: tier
      operator: Equal
      value: frontend
      effect: NoSchedule
  containers:
    - image: nginx
      name: nginx
      resources: { }
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: { }
```

### Create a pod that will be placed on node controlplane. Use nodeSelector and tolerations.

```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yml
# Edit nginx-pod.yml, add nodeSelector: controlplane to the spec
kubectl apply -f nginx-pod.yml
```

The nginx-pod.yml will look like this

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  nodeSelector:
    node-role.kubernetes.io/control-plane: ""
  containers:
    - image: nginx
      name: nginx
      resources: { }
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: { }
```

## Deployments

kubernetes.io > Documentation > Concepts > Workloads > Workload
Resources > [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment)

### Create a deployment with image nginx:1.18.0, called nginx, having 2 replicas, defining port 80 as the port that this container exposes (don't create a service for this deployment)
```bash
kubectl create deploy --image=nginx:1.18.0 nginx --replicas=2 --port=80
```

### View the YAML of this deployment
```bash
kubectl get deploy nginx -o yaml
```

### View the YAML of the replica set that was created by this deployment
```bash
kubectl get rs -l app=nginx
```

### Get the YAML for one of the pods
```bash
kubectl get po nginx-5bfb4b8bd8-d74gw -o yaml
```

### Check how the deployment rollout is going
```bash
kubectl rollout status deploy nginx
```

### Update the nginx image to nginx:1.19.8
```bash
kubectl set image deploy/nginx nginx=nginx:1.19.8
```

### Check the rollout history and confirm that the replicas are OK
```bash
kubectl rollout history deploy nginx
kubectl get rs -l app=nginx
```

### Undo the latest rollout and verify that new pods have the old image (nginx:1.18.0)
```bash
kubectl rollout undo deploy nginx
kubectl get po -o jsonpath="{.items[*].spec.containers[*].image}"
```

### Do an on-purpose update of the deployment with a wrong image nginx:1.91
```bash
kubectl set image deploy nginx nginx=nginx:1.91
```

### Verify that something's wrong with the rollout
```bash
kubectl rollout status deploy nginx
```

### Return the deployment to the second revision (number 2) and verify the image is nginx:1.19.8
```bash
kubectl rollout undo deploy nginx --to-revision=2
kubectl get po -o jsonpath="{.items[*].spec.containers[*].image}"
```

### Check the details of the fourth revision (number 4)
```bash
kubectl rollout history deploy nginx --revision=4
```

### Scale the deployment to 5 replicas
```bash
kubectl scale deploy nginx --replicas=5
```

### Autoscale the deployment, pods between 5 and 10, targeting CPU utilization at 80%
```bash
kubectl autoscale deployment nginx --cpu-percent=80 --min=5 --max=10
```

### Pause the rollout of the deployment
```bash
kubectl rollout pause deploy nginx
```

### Update the image to nginx:1.19.9 and check that there's nothing going on, since we paused the rollout
```bash
kubectl set image deploy nginx nginx=nginx:1.19.9
kubectl rollout status deploy nginx
```

### Resume the rollout and check that the nginx:1.19.9 image has been applied
```bash
kubectl rollout resume deploy nginx
kubectl get po -o jsonpath="{.items[*].spec.containers[*].image}"
```

### Delete the deployment and the horizontal pod autoscaler you created
```bash
kubectl delete deploy nginx
kubectl delete hpa nginx
```

### Implement canary deployment by running two instances of nginx marked as version=v1 and version=v2 so that the load is balanced at 75%-25% ratio
Nginx 1:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
    version: v1
  name: nginx1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
        resources: {}
        volumeMounts:
          - name: workdir
            mountPath: /usr/share/nginx/html
      initContainers:
      - name: install
        image: busybox:1.28
        command:
          - /bin/sh
          - -c
          - "echo version-1 > /work-dir/index.html"
        volumeMounts:
          - name: workdir
            mountPath: "/work-dir"
      volumes:
        - name: workdir
          emptyDir: { }
status: {}
```

Nginx 2:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
    version: v2
  name: nginx2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
        resources: {}
        volumeMounts:
          - name: workdir
            mountPath: /usr/share/nginx/html
      initContainers:
      - name: install
        image: busybox:1.28
        command:
          - /bin/sh
          - -c
          - "echo version-2 > /work-dir/index.html"
        volumeMounts:
          - name: workdir
            mountPath: "/work-dir"
      volumes:
        - name: workdir
          emptyDir: { }
status: {}
```

Nginx svc:
```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx-svc
spec:
  ports:
  - name: 80-80
    port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
  selector:
    app: nginx
  type: NodePort
status:
  loadBalancer: {}
```

## Jobs
### Create a job named pi with image perl:5.34 that runs the command with arguments "perl -Mbignum=bpi -wle 'print bpi(2000)'"
### Wait till it's done, get the output
```bash
kubectl create job pi --image=perl:5.34 -- perl -Mbignum=bpi -wle 'print bpi(2000)'
kubectl get po
kubectl logs pi-5c7cr
```

### Create a job with the image busybox that executes the command 'echo hello;sleep 30;echo world'
```bash
kubectl create job busybox --image=busybox -- /bin/sh -c "echo hello;sleep 30;echo world"
```

### Follow the logs for the pod (you'll wait for 30 seconds)
```bash
kubectl get po
kubectl logs -f busybox-9hzg5
 ```

### See the status of the job, describe it and see the logs
```bash
kubectl get job busybox
kubectl describe job busybox
kubectl logs busybox-9hzg5
```

### Delete the job
```kubectl
kubectl delete job pi
kubectl delete job busybox
```

### Create the same job, make it run 5 times, one after the other. Verify its status and delete it
```bash
kubectl create job busybox --image=busybox --dry-run=client -o yaml -- /bin/sh -c "echo hello;sleep 30;echo world" > jobs.yaml
# Add completions: 5 into spec
kubectl get jobs
kubectl delete job busybox
```

### Create the same job, but make it run 5 parallel times
```bash
kubectl create job busybox --image=busybox --dry-run=client -o yaml -- /bin/sh -c "echo hello;sleep 5;echo world" > jobs.yaml
# Add parallelism: 5 into spec
kubectl get jobs
kubectl delete job busybox
```

### Create a job but ensure that it will be automatically terminated by kubernetes if it takes more than 30 seconds to execute
```bash
kubectl create job busybox --image=busybox --dry-run=client -o yaml -- /bin/sh -c "echo hello;sleep 30;echo world" > jobs.yaml
# Add activeDeadlineSeconds: 30 into spec
kubectl get jobs
kubectl delete job busybox
```

## Cron jobs
kubernetes.io > Documentation > Tasks > Run Jobs > [Running Automated Tasks with a CronJob](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/)

### Create a cron job with image busybox that runs on a schedule of "*/1 * * * *" and writes 'date; echo Hello from the Kubernetes cluster' to standard output
```bash
kubectl create cj busybox --image=busybox --schedule='*/1 * * * *' -- bin/sh -c "date; echo Hello from the Kubernetes cluster"
```

### See its logs and delete it
```bash
kubectl get pods
kubectl logs <pod-name>
kubectl delete cj busybox
```

### Create the same cron job again, and watch the status. Once it ran, check which job ran by the created cron job. Check the log, and delete the cron job
```bash
kubectl create cj busybox --image=busybox --schedule='*/1 * * * *' -- bin/sh -c "date; echo Hello from the Kubernetes cluster"
kubectl get cj --watch
kubectl get jobs
kubectl logs job/<job-name>
kubectl delete cj busybox
```

### Create a cron job with image busybox that runs every minute and writes 'date; echo Hello from the Kubernetes cluster' to standard output. The cron job should be terminated if it takes more than 17 seconds to start execution after its scheduled time (i.e. the job missed its scheduled time).
```bash
kubectl create cj busybox --image=busybox --schedule='*/1 * * * *' --dry-run=client -o yaml -- bin/sh -c "date; echo Hello from the Kubernetes cluster" > cj.yaml
# Add startingDeadlineSeconds: 17 into spec
kubectl apply -f cj.yaml
```

### Create a cron job with image busybox that runs every minute and writes 'date; echo Hello from the Kubernetes cluster' to standard output. The cron job should be terminated if it successfully starts but takes more than 12 seconds to complete execution.
```bash
kubectl create cj busybox --image=busybox --schedule='*/1 * * * *' --dry-run=client -o yaml -- bin/sh -c "date; echo Hello from the Kubernetes cluster" > cj.yaml
# Add activeDeadlineSeconds: 12 into spec.jobTemplate.spec
kubectl apply -f cj.yaml
```

### Create a job from cronjob.
```bash
kubectl create cj busybox --image=busybox --schedule='*/1 * * * *' --dry-run=client -o yaml -- bin/sh -c "date; echo Hello from the Kubernetes cluster" > cj.yaml
kubectl create job --from=cj/busybox
```