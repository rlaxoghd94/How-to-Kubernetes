# Namespaces

Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called *namespaces*.

## When to Use Multiple Namespaces

Namespaces are intended for use in environment with many users spread across multiple teams, or projects. For clusters with a few to tens of users, you shouldn't need to create or think about namespaces at all. Start using namespaces when you need the features they provide.

Namespaces provide a scope for names. Names of resources need to be unique within a namespaces, but not across namespaces. Namespaces cannot be nested inside one another and each Kubernetes resource can only be in one namespace.

> Namespaces are a way to divide cluster resources between multiple users

## Working with Namespaces

### Viewing namespaces

You can list the current namepaces in a cluster using:

```bash
kubectl get namespace

NAME          STATUS    AGE
default       Active    1d
kube-system   Active    1d
kube-public   Active    1d
```

Kubernetes starts with three initial namespaces:

- `default`: The default namespace for objects with no other namespace

- `kube-system`: The namepsace for objects created by the Kubernetes system

- `kube-public`: This namespace is created automatically and is readable by all users(including those not authenticated). This namespace is mostly reserved for cluster usage, in case that some resources should be visible and readable publicly througout the whole cluster. The public aspect of this namespace is only a convention, not a requirement

### Setting the namespace for a request

To set the namespace for a current request, use the `--namespace` flag.

For example:

```bash
kubectl run nginx --image=nginx --namespace=<insert-namespace-name-here>
kubectl get pods --namespace=<insert-namespace-name-here>
```

You can permanently save the namepsace for all subsequent kubectl commands in that context.

```bash
kubectl config set-context --current --namespace=<insert-namespace-name-here>
# Validate it
kubectl config view --minify | grep namespace:
```

## Namespace and DNS

When you create a Service, it creates a corresponding DNS entry. This entry is of the form `<service-name>.<namespace-name>.svc.cluster.local`, which means that if a container just uses `<service-name>`, it will resolve to the service which is local to a namespace. This is useful for using the same configuration across multiple namespaces such as Development, Staging and Production.

## Not All Objects are in a Namespace

Most Kubernetes resources(e.g. pods, services, replication controllers, and others) are in some namespaces. However namespace resources are not themselves in a namespace. And low-level resources, such as `nodes` and `persistentVolumes`, are not in any namespace.

To see which Kubernetes resources are and aren't in a namespace:

```bash
# In a namespace
kubectl api-resources --namespaced=true

# Not in a namespace
kubectl api-resources --namespaced=false
```