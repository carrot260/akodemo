# Atlas Kubernetes Operator Demo

### Prerequisites:

1. Install kubectl command line tool. Please refer to kubernetes documentation for exact steps (https://kubernetes.io/docs/tasks/tools/)
2. Install minikube. Please refer to minikube documentation for exact steps (https://minikube.sigs.k8s.io/docs/start/)
3. Create an Atlas Project.
4. Create API keys for the Atlas Project (Step 3)

### Steps:

1. Deploy Atlas Kubernetes Operator.
You can run the following command to deploy the latest operator
```
kubectl apply -f https://raw.githubusercontent.com/mongodb/mongodb-atlas-kubernetes/main/deploy/all-in-one.yaml
```
If you have already cloned the repo locally, you can deploy the operator using the `all-in-one.yaml` file (mongodb-atlas-kubernetes/deploy/all-in-one.yaml directory).

2. Check the namespace using the following command
```
kubectl get namespace
```
3. Set context using the following command. Use `--namespace` option to specify namespace. (eg: mongodb-atlas-system)
```
kubectl config set-context --current --namespace=mongodb-atlas-system
```

4. List all pods using the following command
```
kubectl get pods
```

5. Tail operator log using the following command. Use `-n` to specify namespace (eg: mongodb-atlas-system)
```
kubectl logs -n mongodb-atlas-system deployment/mongodb-atlas-operator -f
```

6. Specify API credentials using the following command. Replace `<orgId>`, `<publicApiKey>`, `<privateApiKey>`. Use `-n` to specify namespace (eg: mongodb-atlas-system)
```
kubectl create secret generic mongodb-atlas-operator-api-key \
    --from-literal="orgId=<orgId>" \
    --from-literal="publicApiKey=<publicApiKey>" \
    --from-literal="privateApiKey=<privateApiKey>" \
    -n mongodb-atlas-system
```

7. Label Secret using the following command. Specify a secret name (eg: mongodb-atlas-operator-api-key). Use `-n` to specify namespace.
```
kubectl label secret mongodb-atlas-operator-api-key atlas.mongodb.com/type=credentials -n mongodb-atlas-system
```

8. Create Atlas Project using the following command. `atlasproject.yaml` file can be found under `carrot260/akodemo/atlasproject.yaml`
```
kubectl apply -f atlasproject.yaml
```

9. Create Atlas Cluster using the following command. `atlascluster.yaml` file can be found under `carrot260/akodemo/atlascluster.yaml`
```
kubectl apply -f atlascluster.yaml
```

10. Create database user using following steps:

10(a): Create a secret for database user password.
```
kubectl create secret generic mysupersecretpassword --from-literal="password=secre@tpassword"
```
10(b): Label secret.
```
kubectl label secret mysupersecretpassword atlas.mongodb.com/type=credentials
```
10(c): Create database user using the following command. `atlasdatabaseuser.yaml` file can be found under `carrot260/akodemo/atlasdatabaseuser.yaml`
```
kubectl apply -f atlasdatabaseuser.yaml
```

11. Delete resources using the following steps:

11(a): Delete atlas cluster:
```
kubectl delete -f atlascluster.yaml
```

11(b): Delete database user:
```
kubectl delete -f atlasdatabaseuser.yaml
```

11(c): Delete database user secret
```
kubectl delete secret mysupersecretpassword
```

11(d): Delete Atlas project:
```
kubectl delete -f atlasproject.yaml
```