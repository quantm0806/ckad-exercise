## Helm in K8s

### Creating a basic Helm chart
```bash
helm create basic
```

### Running a Helm chart
```bash
helm install basic ./basic
```

### Find pending Helm deployments on all namespaces
```bash
helm list --pending -A
```

### Uninstall a Helm release
```bash
helm uninstall basic
```

### Upgrading a Helm chart
```bash
helm upgrade basic basic --set replicaCount=2 --set image.tag=1.29
helm get values basic
```

### Using Helm repo
```bash
helm repo list
```

### Download a Helm chart from a repository
```bash
helm pull <repo_name>/<chart_name>
```

### Add the Bitnami repo at https://charts.bitnami.com/bitnami to Helm
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

### Write the contents of the values.yaml file of the bitnami/node chart to standard output
```bash
helm show values bitnami/node
```

### Install the bitnami/node chart setting the number of replicas to 5
```bash
helm install node bitnami/node --set replicaCount=5
```