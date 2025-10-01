Task 1:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: febe 
  namespace: ps
spec:
  podSelector:
    matchLabels:
      app: be
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: fe
    ports:
    - protocol: TCP
      port: 5432

```

Task 2:
```bash
# 1:
vi febe-netpol.yml
kubectl apply -f febe-netpol.yml
kubectl describe netpol febe -n ps

# 2: See febe-netpol.yml
```