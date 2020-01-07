# Minikube Setup

## Minikube Feature

Minikube supports the following Kubernetes features:

- DNS

- NodePorts

- ConfigMaps and Secrets

- Dashboards

- Container Runtime: Docker, CRI-O, and containerd

- Enabling CNI(Container Network Interface)

- Ingress

## Quickstart

This breif demo guides you on how to start, use, and delete `minikube` locally. Follow the steps given below to start and explore Minikube:

1. Start Minikube and create a cluster:

```bash
minikube start
```

2. Now, you can interact with your cluster using `kubectl`.

Let's create a Kubenetes Deployment using an existing image named `echoserver`, which is a simple HTTP server and expose it on port 8080 using --port

```bash
kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.10

# expected output
deployment.apps/hello-minikube created
```

3. To access the `hello-minikube` Deployment, expose it as a Service:

```bash
kubectl expose deployment hello-minikube --type=NodePort --port=8080

# expected output
service/hello-minikube exposed
```

The option `--type=NodePort` specifies the type of the Service.

4. The `hello-minikube` Pod is now launched but you have to wait until the Pod is up before accessing it via the exposed Service.

Check if the Pod is up and running:

```bash
kubectl get pod

# expected output
NAME                              READY     STATUS              RESTARTS   AGE
hello-minikube-3383150820-vctvh   0/1       ContainerCreating   0          3s
```

The `ContainerCreating` status will turn into `Running` in a moment.

5. Get the URL of the exposed Service to view the Service details:

```bash
minikube service hello-minikube --url

# expected output
http://192.168.99.100:30074
```

6. To view the details of your local cluster, copy and paste the URL you got as the output, on your browser.

The output is similar to this:

```
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

```bash
kubectl delete services hello-minikube

# expected output
service "hello-minikube" deleted
```

8. Delete the `hello-minikube` Deployment:

```bash
kubectl delete deployment hello-minikube

# expected output
deployment.extensions "hello-minikube" deleted
```

9. Stop the local Minikube cluster:

```bash
minikube stop

# expected output
Stopping "minikube"...
"minikube" stopped.
```

10. Delete the local Minikube cluster:

```bash
minikube delete

# expected output
Deleting "minikube" ...
The "minikube" cluster has been deleted.
```
