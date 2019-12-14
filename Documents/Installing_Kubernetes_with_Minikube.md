# Installing Kubernetes with Minikube

Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a *single-node* Kubernetes cluster inside a Virtual Machine(VM) on your laptop for users looking to try out Kubernetes or develop with it day-to-day.

## Minikube Features

Minikube supports the following Kubernetes features:

- DNS

- NodePorts

- ConfigMaps and Secrets

- Dashboards

- Container Runtime: Docker, CRI-O, and containerd

- Enabling CNI (Container Network Interface)

- Ingress

## Installation

See [Installing Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)

## Quickstart

This brief demo guides you on how to start, use, and delete Minikube locally. Follow the given stops below to start and and explore Minikube.

1. Start Minikube and create a cluster:

```sh
minikube start
```

The output is similar to this:

```sh
Starting local Kubernetes cluster...
Running pre-create checks...
Creating machine...
Starting local Kubernetes cluster...
```

2. Now you can interact with your cluster using kubectl.

Lets' create a Kuberenetes Deployment using an existing image named `echoserver`, which is a simple HTTP server and expose it on port 8080 using `--port`.

```sh
kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.10
```

The output is similar to this:

```sh
deployment.apps/hello-minikube created
```

3. To access the `hello-minikube` Deployment, expose it as a Service:

```sh
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

The option `--type=NodePort` specifies the type of the Service.

The ouput is similar to this:

```sh
service/hello-minikube exposed
```

4. The `hello-minikube` Pod is now launched but you have to wait until the Pod is up before accessing it via the exposed Service.

Check if the Pod is up and running

```sh
kubectl get pod
```

If the output shows the `STATUS` as `ContainerCreating`, the Pod is still being created:

```sh
NAME                              READY     STATUS              RESTARTS   AGE
hello-minikube-3383150820-vctvh   0/1       ContainerCreating   0          3s
```

If the output shows the `STATUS` as `Running`, the Pod is now up and running:

```sh
NAME                              READY     STATUS    RESTARTS   AGE
hello-minikube-3383150820-vctvh   1/1       Running   0          13s
```

5. Get the URL of the exposed Service to view the Service details:

```sh
kubectl service hello-minikube --url
```

6. To view the details of your local cluster, copy and paste the URL you got as the output, on your browser.

The output is similar to this:

```sh
Hostname: hello-minikube-7c77b68cff-8wdzq

Pod Information:
    -no pod information available-

Server values:
    server_version=nginx: 1.13.3 - lua: 10008

Request Information:
    client_address=172.17.0.1
    method=GET
    real path=/
    query=
    request_version=1.1
    request_scheme=http
    request_uri=http://192.168.99.100:8080/

Request Headers:
    accept=*/*
    host=192.168.99.100:30674
    user-agent=curl/7.47.0

Request Body:
    -no body in request-
```

If you no longer want the Service and cluster to run, you can delete them.

7. Delete the `hello-minikube` Service:

```sh
kubectl delete services hello-minikube
```

The output is similar to this:

```sh
service "hello-minikub" deleted
```

8. Delete the `hello-minikube` Deployment:

```sh
kubectl delete deployment hello-minikube
```

The output is similar to this:

```sh
deployment.extensions "hello-minikube" deleted
```

9. Stop the local Minikube cluster:

```
minikube stop
```

The output is similar to this:

```sh
Stopping "minikube"...
"minikube" stopped.
```

10. Delete the local Minikube cluster

```sh
minikube delete
```

The output is similar to this:

```sh
Deleting "minikube" ...
The "minikube" cluster has been deleted.
```

## Managing your cluster

### Specifying the VM driver

You can change the VM driver by adding the `--vm-driver=<enter_driver_name>` flag to `minikube start`.

List of Drivers are:

- virtualbox

- vmwarefusion

- kvm2

- hyperkit

- hyperv

- vmware

### User local Images by re-using the Docker daemon

When using a single VM for Kubernetes, it's useful to reuse Minikube's built-in Docker daemon. Resuing the built-in daemon means you don't have to build a Docker registry on your host maching and push the image into it. Instead, you can build inside the same Docker daemon as Minikube, which speeds up local experiments.

**Note**: Be sure to *tag* your Docker image with something other than latest and use that tag to pull the image. Because `:latest` is the default value, with a corresponding default image pull policy of `Always`, an image pull error (`ErrImagePull`) evenetually results if you do not have the Docker image in the default Docker registry (usually DockerHub).

To work with the Docker daemon on your Mac/Linux host, use the `docker-env command` in your shell:

```sh
eval $(minikube docker-env)
```

You can now use Docker at the command line of your host Mac/Linux machine to communicate with the Docker daemon inside the Minikube VM:

```sh
docker ps
```

### Interacting with Your Cluster

#### Kubectl

The `minikube start` command creates a kubectl context called "minikube". This context contains the configuration to communicate with your Minikube cluster.

Minikube sets this context to default automatically, but if you need to switch back to it in the future, run:

```sh
kubectl config use-context minikube
```

Or pass the context on each command like this: `kubectl get pods --context=minikube`

#### Dashboard

To access the Kubernetes Dashboard, run this command in a shell after starting Minikube to get the address:

```sh
minikube dashboard
```

#### Service

To access a Service exposed via a node port, run this command in a shell after starting Minikube to get the address:

```sh
minikube service [-n NAMESPACE] [--url] NAME
```

