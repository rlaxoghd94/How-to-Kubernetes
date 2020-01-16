# HA Stacked Etcd Cluster Initialization

So, the goal of this initialization sequence is to configure a HA Stacked Etcd Cluster with the following components:

- 1 - Loadbalancer(HAProxy) : 192.168.10.10

- 3 - Master Nodes : 192.168.10.(11~13)

- 1 - Worker Node : 192.168.10.14

<br></br>

## Setting up the Primary Master Node

Among the three master nodes available, one has to be the *primary* master node whereas the other two are the *secondary* master nodes.

In order to initialize the primary master node, you need execute the following command:

```bash
sudo kubeadm init --control-plane-endpoint "192.168.10.10:6443" --upload-certs --apiserver-advertise-address=192.168.10.11


# Expected Output
* * *

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of the control-plane node running the following command on each as root:

  kubeadm join 192.168.10.10:6443 --token 6jukrs.6xisfqikp0z1f0bz \
    --discovery-token-ca-cert-hash sha256:b38049c370cc5aa9d0449720b78175663a85599a1c7f313b4a4124fbf147e931 \
    --control-plane --certificate-key e3f7a22e44b00b4893d2b9231cdd5bc83ec860c389c165e32c21806a85763973

Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use
"kubeadm init phase upload-certs --upload-certs" to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.10.10:6443 --token 6jukrs.6xisfqikp0z1f0bz \
    --discovery-token-ca-cert-hash sha256:b38049c370cc5aa9d0449720b78175663a85599a1c7f313b4a4124fbf147e931 

```

You may set the CNI plug-in at this stage(it's optional at the moment).

The difference you'll have is the `core-dns` pods not being ready if you don't have the CNI plug-in installed(Check using `kubectl get pods --all-namespaces`).

The important aspect of the command above is the `--apiserver-advertise-address=192.168.10.11` option. Without this, I've found the secondary master nodes not being able to find the cluster and the primary master node.

<br></br>

## Setting up the Secondary Master Nodes

Now, I've encountered a few trouble during this process.

The problem was that the VM kept on searching for it's own local ip address, and not the private network that has been hard-set in the Vagrant file. This can be simply solved by:

```bash
sudo route add default gw {ip-addr-of-the-secondary-master-node}
```

But then, you'll encounter *another* problem where your VM cannot access your host machine's network and can't download the kubernetes docker images.

Here's what I've done to overcome the problems:

1. Use `sudo kubeadm config images pull` to download the images

2. Use `sudo route add default gw {vm-private-ip}`

3. Use the `kubeadm join` command specified below

Follow the command given to you during the primary master node initialization:

```bash
kubeadm join 192.168.10.10:6443 --token 6jukrs.6xisfqikp0z1f0bz \
    --discovery-token-ca-cert-hash sha256:b38049c370cc5aa9d0449720b78175663a85599a1c7f313b4a4124fbf147e931 \
    --control-plane --certificate-key e3f7a22e44b00b4893d2b9231cdd5bc83ec860c389c165e32c21806a85763973

# Expected Output
* * *

This node has joined the cluster and a new control plane instance was created:

* Certificate signing request was sent to apiserver and approval was received.
* The Kubelet was informed of the new secure connection details.
* Control plane (master) label and taint were applied to the new node.
* The Kubernetes control plane instances scaled up.
* A new etcd member was added to the local/stacked etcd cluster.

To start administering your cluster from this node, you need to run the following as a regular user:

	mkdir -p $HOME/.kube
	sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
	sudo chown $(id -u):$(id -g) $HOME/.kube/config

Run 'kubectl get nodes' to see this node join the cluster.
```

<br></br>

## Setting up the Worker Node

This process is straight-foward. Follow the command given to you during the primary master node initialization:

```bash
kubeadm join 192.168.10.10:6443 --token 6jukrs.6xisfqikp0z1f0bz \
    --discovery-token-ca-cert-hash sha256:b38049c370cc5aa9d0449720b78175663a85599a1c7f313b4a4124fbf147e931
```

<br></br>

## Result

You'll have a fully working HA Stacked Etcd Cluster at hand!

```bash
kubectl get nodes

NAME     STATUS     ROLES    AGE   VERSION
node-1   Ready      master   23m   v1.17.0
node-2   Ready      master   22m   v1.17.0
node-3   NotReady   master   21m   v1.17.1
worker   Ready      <none>   20m   v1.17.0
```

```bash
kubectl get pods --all-namespaces

NAMESPACE     NAME                             READY   STATUS              RESTARTS   AGE
kube-system   coredns-6955765f44-jg25t         0/1     ContainerCreating   0          3m6s
kube-system   coredns-6955765f44-rzv5r         0/1     ContainerCreating   0          3m6s
kube-system   etcd-node-1                      1/1     Running             0          3m12s
kube-system   etcd-node-2                      1/1     Running             0          2m4s
kube-system   etcd-node-3                      1/1     Running             0          83s
kube-system   kube-apiserver-node-1            1/1     Running             0          3m12s
kube-system   kube-apiserver-node-2            1/1     Running             0          2m5s
kube-system   kube-apiserver-node-3            0/1     Pending             0          2s
kube-system   kube-controller-manager-node-1   1/1     Running             1          3m12s
kube-system   kube-controller-manager-node-2   1/1     Running             0          2m5s
kube-system   kube-controller-manager-node-3   1/1     Running             0          13s
kube-system   kube-proxy-5xxwl                 1/1     Running             0          3m6s
kube-system   kube-proxy-6m7fv                 1/1     Running             0          2m6s
kube-system   kube-proxy-7gpj4                 1/1     Running             0          13s
kube-system   kube-proxy-wqft9                 1/1     Running             0          85s
kube-system   kube-scheduler-node-1            1/1     Running             1          3m12s
kube-system   kube-scheduler-node-2            1/1     Running             0          2m5s
kube-system   kube-scheduler-node-3            1/1     Running             0          6s
```