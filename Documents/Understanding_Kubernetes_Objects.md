# Understanding Kubernetes Objects

This page explains how Kubernetes objects are represented in the Kubernetes API, and how you can express them in `.yaml` format.

## Understanding Kubernetes Objects

*Kubernetes Objects* are persistent entities in the Kubernetes system. Kubetnetes uses these entities to represent the state of your cluster. Specifically, they can describe:

- What containerized applications are running(and on which nodes)

- The resources available to those applications

- The policies around how those applications behave, such as restart policies, upgrades, and fault-tolerance

A Kubernetes object is a "record of intent" - once you create the object, the Kubernetes system will constantly work to ensure that object exists. By creating an object, you're effectively telling the Kubernetes system what you want your cluster's workload to look like; this is your cluster's *desired state*.

To work with Kubernetes object - whether to create, modify, or delete them - you'll need to use the Kubernetes API. When you use the `kubectl` command-line interface, for example, the CLI makes the necessary Kubernetes API calls for you. You can also use the Kubernetes API directly in your own programs using one of the CLient Libraries.

### Object Spec and Status

Every Kubernetes object includes two nested object fields that govern the object's configuration: the object *spec* and the object *status*. The *spec*, which you must provide, describes your desired state for the object - the characteristics that you want the object to have. The *status* describes the actual state of the object, and is supplied and updated by the Kubernetes system. At any given time, the Kubernetes Control Plane actively manages an object's actual state to match the desired state you supplied.

For example, a Kubernetes Deployment is an object that can represent an application running on your cluster. When you create the Deployment, you might set the Deployment spec to specify that you want three replicas of the application to be running. The Kubernetes system reads the Deployment spec and starts three instances of your desired application - updating the status to match your spec. If any of those instances should fail(a status change), the Kubernetes system responds to the difference between spec and status by making a correction - in this case, starting a replacement instance.

### Describing a Kubernetes Object

When you create an object in Kubernetes, you must provide the object spec that describes its desired state, as well as some basic information about the object(such as a name). When you use the Kubernetes API to create the object(either directly or via `kubectl`), that API request must include that information as JSON in the request body. **Most often, you provide the information to `kubectl` in a .yaml file**. `kubectl` converts the information to JSON when making the API request.

Here's an example `.yaml` file that shows the required fields and object spec for a Kubernetes Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metatdata:
    name: nginx-deployment
spec:
    selector:
        matchLabels:
            app: nginx
    replicas: 2 # tells deployment to run 2 pods matching the template
    template:
        metadata:
            labels:
                app: nginx
        spec:
            containers:
            -  name: nginx
               image: nginx:1.7.9
               ports:
               -  containerPort: 80
```

One way to create a Deployment using a `.yaml` file like the one above is to use the `kubectl apply` command in the `kubectl` CLI, passing the `.yaml` file as an argument.

Here's an example:

```sh
kubectl apply -f https://k8s.io/examples/application/deployment.yaml --record
```

The output is similar to this:

```sh
deployment.apps/nginx-deployment created
```

### Required Fields

in the `.yaml` file for the Kubernetes object you want to create, you'll need to set values for the following fields:

- `apiVersion` - Which version of the Kubernetes API you're using to create this object

- `kind` - What kind of object you want to create

- `metadata` - Data that helps uniquely identify the object, including a `name` string, `UID`, and optional `namespace`

- `spec` - What state you desire for the object

The precise format of the object `spec` is different for eveery Kubernetes object, and contains nested fields specific to that object