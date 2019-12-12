# Namespaces

Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces.

## When to Use Multiple Namespaces

Namespaces are intended for use in environments with many users spread across multiple teams, or projects. For clusters with a few to tens of users, you should not need to create or think about namespaces at all. Start using namespaces when you need the features they provide.

Namespaces provide a scope for names. Names of resources need to be unique within a namespacce, but not across namespaces. Namespaces can not be nested inside one another and each Kubernetes resources can only be in one namespace.

Namespaces are a way to divide cluster resources between multiple users.

It is not necessary to use multipl enmaespaces just to separate slightly different resources, such as different versions of the same software; use labels to distinguish resources within the same namespace.

<br></br>

## Working with Namespaces

### Viewing namespaces

You can list the current namespaces in a cluster using:

```sh
kubectl get namespace
```

```sh
NAME          STATUS    AGE
default       Active    1d
kube-system   Active    1d
kube-public   Active    1d
```

Kubernetes starts with three initial namespaces:

- `default` The default namespace for objects with no other namespace

- `kube-system` The namespace for objects created by the Kubernetes system

- `kube-public` This namespace is created automatically and is readable by all users(including those not authenticated). This namespace is mostly reserved for cluster usage, in case that some resources should be visible and readable publicly throughout the whole cluster. The public aspect of this namespace is only a convention, not a requirement

### Setting the namespace for a request

To set the namespacce for a current requests, use the `--namespace` flag

For example:

```sh
kubectl run nginx --image=nginx --namespace=<insert-namespace-name-here>
kubectl get pods --namespace=<insert-namespace-name-here>
```

### Setting the namespace preference

You can permanently save the namespace for all subsequent kubectl commands in that context.

```sh
kubectl config set-context --current --namespace=<insert-namespace-name-here>
# Validate it
kubectl config view --minify | grep namespace:
```

<br></br>

## Namespaces and DNS

When you create a Service, it creates a corresponding DNS entry. This entry is of the form `<service-name>`, `<namespace-name>`, `svc.cluster.local`, which means that if a container just uses `<service-name>`, it will resolve to the service which is local to a namespace. This is useful for using the same configuration across multiple namespaces such as Development, Staging, and Production. If you want to reach across namespaces, you need to use the fully qualified domain name(FQDN).

<br></br>

## Not All Objects are in a Namespace

Most Kubernetes resources(e.g. pods, services, replicatoin controllers, and others) are in some namespaces. However, namespace resources are not themselves in a namespace. And low-level resources, such as nodes and persistentVolumes, are not in any namespace.

To see which Kubernetes resources are and aren't in a namespace:

```sh
# In a namespace
kubectl api-resources --namespaced=true

# Not in a namespace
kubectl api-resources --namespaced=false
```