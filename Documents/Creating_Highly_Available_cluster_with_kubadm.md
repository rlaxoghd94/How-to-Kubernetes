# Creating Highly Available clusters with kubeadm

This page explains two different approaches to setting up a highly available Kubernetes cluster using kubeadm:

- With stacked control plane nodes. This approach requires less infrastructure. The etcd members and control plane nodes are co-located.

- With an external etcd cluster. This approach requires more infrastructure. The control plane nodes and etcd members are separated.

<br></br>

## First steps for both methods

### Create load balancer for kube-apiserver

1. Create a kube-apiserver load balancer with a name that resolved to DNS

2. Add the first control plane nodes to the load balancer and test the connection:

```sh
nc -v LOAD_BALANACER_IP PORT
```

<br></br>

## Stacked control plane and etcd nodes

### Steps for the first control plane node

1. Initialize the control plane:

```sh
sudo kubeadm init --control-plane-endpoint "LOAD_BALANCER_DNS:LOAD_BALANCER_PORT" --upload-certs
```

- You can use the `--kubernetes-version` flag to set the Kubernetes version to use. It is recommended that the versions of kubeadm, kubelet, kubectl and Kubernetes match

- The `--control-plane-endpoint` flag should be set to the address or DNS and port of the load balancer

- The `--upload-certs` flag is used to upload the certificates that should be shared across all the control-plane instances to the cluster.

- The output looks similar to:

```sh
...
You can now join any number of control-plane node by running the following command on each as a root:
kubeadm join 192.168.0.200:6443 --token 9vr73a.a8uxyaju799qwdjv --discovery-token-ca-cert-hash sha256:7c2e69131a36ae2a042a339b33381c6d0d43887e2de83720eff5359e26aec866 --control-plane --certificate-key f8902e114ef118304e561c3ecd4d0b543adc226b7a07f675f56564185ffe0c07

Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use kubeadm init phase upload-certs to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:
kubeadm join 192.168.0.200:6443 --token 9vr73a.a8uxyaju799qwdjv --discovery-token-ca-cert-hash sha256:7c2e69131a36ae2a042a339b33381c6d0d43887e2de83720eff5359e26aec866
```

- Copy this output to a text file. You will need it later to join control plane and worker nodes to the cluster

- When `--upload-certs` is used with `kubeadm init`, the certificates of the primary control plane are encrypted and uploaded in the `kubeadm-certs` Secret

- To re-upload the certificates and generate a new decryption key, use the floowing command on a control plane node that is already joined to the cluster:

```sh
sudo kubeadm init phase upload-certs --upload-certs
```

- You can also specify a custom `--certificate-key` during `init` that can later be used by `join`. To generate such a key you can use the following command:

```sh
kubeadm alpha certs certificate-key
```

2. Apploy the CNI plugin of you choice. Make sure the configuration corresponds to the Pod CIDR specified in the kubeadm configuration file if applicable. ([Instruction](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network))

3. Type the following and watch the pods of the control plane components get started:

```sh
kubectl get pod -n kube-system -w
```

### Steps for the rest of the control plane nodes

For each additional control plane node you should:

1. Execute the join command that was previously given to you by the `kubeadm init` output on the first node. It should look something like this:

```sh
sudo kubeadm join 192.168.0.200:6443 --token 9vr73a.a8uxyaju799qwdjv --discovery-token-ca-cert-hash sha256:7c2e69131a36ae2a042a339b33381c6d0d43887e2de83720eff5359e26aec866 --control-plane --certificate-key f8902e114ef118304e561c3ecd4d0b543adc226b7a07f675f56564185ffe0c07
```

- The `--control-plane` flag tells `kubeamd join` to create a new control plane

- the `--certificate-key ...` will cause the control plane certificates to be downloaded from the `kubeadm-certs` Secret in the cluster and be decrypted using the given key.

<br></br>

## External etcd nodes
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/#external-etcd-nodes