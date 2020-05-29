
# Reference on using Kubernetes

This is not a guide nor tutorial but it serves as a reference on how to use basic functionalities on Kubernetes


## To use Web UI (dashboard)

Reference: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

1. Run kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
2. Create ServiceAccount followed by ClusterRoleBinding for the service account that is just created (refer https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md)
3. Run the following in Powershell to get the token to login:
```
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | sls admin-user | ForEach-Object { $_ -Split '\s+' } | Select -First 1)
```
4. Run kubectl proxy
5. It is now accessible via: http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.


## To create namespace and switch namespace

Namespace is like subgroup in a cluster. It can be used for different groups or projects in the same cluster

1. Refer to /namespace/create-namespace.yaml
2. To create context to point to the namespace like following (Reference: https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/):
```
kubectl config set-context <context-name> --namespace=<namespace-name> \
  --cluster=<cluster-name> \
  --user=<user-group>
```
3. To view context: kubectl config view
4. To view current context: kubectl config current-context
5. To switch context: kubectl config use-context <context-name>


## To scale up / down deployment manually

1. Set the number of replicas in the deployment yaml file and apply it


## To scale up / down deployment using Horizontal Pod Autoscaler 

1. Run the following command. The commmand will create a HPA which will attempt to maintain an average CPU utilization across all pods at 50% with a min of 1 pod to max of 10 pods.
```
kubectl autoscale deployment <deployment_name> --cpu-percent=50 --min=1 --max=10
```

## Using Ingress to expose HTTP and HTTPS routes outside cluster to services within cluster

1. Enable the NGINX Ingress controller on Minikube
```
minikube addons enable ingress
``` 
2. Set the services to be using ClusterIP
3. Apply the Ingress yaml file (Refer to folder /ingress)
```
kubectl apply -f web-app-ingress.yaml
```
4. Now you can access the service via the path