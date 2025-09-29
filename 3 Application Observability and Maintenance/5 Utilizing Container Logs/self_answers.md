```bash
# 1:
kubectl apply -f pod-logging.yaml

# 2:
kubectl get pods -o json | jq '.items[0].status.containerStatuses [].ready'

# 3:
kubectl get pods -o json | jq '.items[0].status.containerStatuses [].name'

# Answer for both 2 and 3 can be: 
kubectl get pods -o json | jq '[.items[0].status.containerStatuses[] | {name: .name, is_ready: .ready}]'

# 4:
kubectl logs counter --all-containers --since=60s

# 5:
kubectl logs -f counter --all-containers

# 6:
kubectl delete -f pod-logging.yaml
```