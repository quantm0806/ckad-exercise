Task 1:
```bash
# 1:
kubectl run busyapp --image=busybox --command -- false

# 2:
kubectl get pod -o json | jq '.items[0].status.containerStatuses[0] | {started: .started, state: .state}'

# 3:
kubectl get events

# 4:
kubectl describe pod busyapp

# 5:
kubectl debug busyapp -it --copy-to=busyapp-debug --container=busyapp -- sh
ls

# 6:
exit

# 7:
kubectl get pods
kubectl delete pod/busyapp pod/busyapp-debug
```