# Creating a single control-plane cluster with Kubeadm

**kubeadm** helps you bootstrap a minimum viable Kubernetes cluster that conforms to best practices. With kubeadm, your cluster should pass Kubernetes Conformance tests. Kubeadm also supports other cluster lifecycle functions, such as upgrades, downgrade, and managing bootstrap tokens.

Because you can install kubeadm on various type of machine, it's well suited for integration with provisioning systems such as Terraform or Ansible.

Kubeadm's simplicity means it can serve a wide range of use cases:

- New users can start with kbueadm to try Kubernetes out for the first time

- Users familiar with Kubernetes can spin up clusters with kubeadm and test their applications

- Larger projects can include kubeadm as a building block in a more complex system that can also include other installer tools

kubeadm is designed to be a simple way for new users to start trying Kubernetes out, possibly for the first time, a way for existing users to test their application on and stitch together a cluster easily, and also to be a building block in other ecosystem and/or installer tool with a larger scope.

## Objectives

- Install a single control-plane Kubernetes cluster or high-availability cluster

- Install a Pod network on the cluster so that your Pods can talk to each other

## Instructions

### Initializing your control-plane node

The control-plane node is the machine where the control plane components run, including etcd(the cluster database) and the API server(which the clubectl CLI communicates with)

1. (Recommended) If you have plans to upgrade this single control-plane kubeadm cluster to high availability you should specify the `--control-plane-endpoint` to set the shared endpoints for all control plane nodes. Such an endpoint can be either a DNS name or an IP address of a load-balancer.

2. Choose a pod network add-on, and verify whether it requires any arguments to be passed to kubeadm initialization. Depending on which third party provider you choose, you might need to set the `--pod-network-cidr` to a provider specific value.

3. (Optional) Since version 1.14, kubeadm will try to detect the container runtime on linux by using a list of well known domain socket paths. To use different container runtime or if there are more than one installed on the provisioned node, specify the `--cri-socket` argument to `kubeadm init`.

4. (Optional) Unless otherwise specified, kubeadm uses the network interface associated with the default gateway to set the advertise address for this particular control-plane node's API server. To use a different network interface, specify the `--apiserver-advertise-address=<ip-address>` argument to `kubeadm init`. To deploy an IPv6 Kubernetes cluster using IPv6 addressing, you must specify an IPv6 address, for example `--apiserver-advertise-address=fd00::101`

5. (Optional) Run `kubeadm config image pull` prior to `kubeadm init` to verify connectivity to gcr.io registries

To initalize the control plane node, run:

```sh
kubeadm init <args>
```

