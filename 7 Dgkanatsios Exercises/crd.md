## CRD in K8s

### Create a CustomResourceDefinition manifest file for an Operator with the following specifications :
```
Name : operators.stable.example.com
Group : stable.example.com
Schema: <email: string><name: string><age: integer>
Scope: Namespaced
Names: <plural: operators><singular: operator><shortNames: op>
Kind: Operator
```

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: operators.stable.example.com
spec:
  group: stable.example.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                email:
                  type: string
                name:
                  type: string
                age:
                  type: integer
  scope: Namespaced
  names:
    plural: operators
    singular: operator
    kind: Operator
    shortNames:
    - op
```

### Create the CRD resource in the K8S API
```bash
kubectl apply -f stable.yaml
```

### Create custom object from the CRD
```
Name : operator-sample
Kind: Operator
Spec:
email: operator-sample@stable.example.com
name: operator sample
age: 30
```

```yaml
apiVersion: "stable.example.com/v1"
kind: Operator
metadata:
  name: "operator-sample"
spec:
  email: "operator-sample@stable.example.com"
  name: "operator sample"
  age: 30
```

```bash
kubectl apply -f my-stable.yaml
```

Listing operator
```bash
kubectl get op
```