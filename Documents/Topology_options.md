# Options for Highly Available topology

This page explains the two options for configuring the topology of your highly available(HA) Kubernetes clusters:

You can set up an HA cluster:

- With ***Stacked*** control plane nodes, where etcd nodes are colocated with control plane nodes

- With ***external*** etcd nodes, where etcd runs on separate nodes from the control plane

You should carefully consider the advantages and disadvatages of each topology before setting up an HA cluster

<br></br>

## Stacked etcd topology

A stacked HA cluster is a topology where the distributed data storage cluster provided by etcd is stacked on top of the cluster formed by the nodes managed by kubeadm that run control components.

Each control plane node runs an instance of the `kube-apiserver`, `kube-scheduler`, and `kube-controller-manager`. The `kube-apiserver` is exposed to worker nodes using a load balancer.

Each control plane node creates a local etcd member and this etcd member communicates only with the `kube-apiserver` of this node. The same applies to the local `kube-controller-manager` and `kube-scheduler` instances.

This topology couples the control planes and etcd members on the same nodes. It is simpler to set up than a cluster with external etcd nodes, and simpler to manage for replication.

However, a stacked cluster runs the risk of failed coupling. If one node goes down, both an etcd member and a control plane instance are lost, and redundancy is compromised. You can mitigrate this risk by adding more control plane nodes.

You should therefore run a *minimum of three* stacked control plane nodes for an HA cluster.

This is the default topology in kubeadm. A local etcd member is created automatically on control plane nodes when using `kubeadm init` and `kubeadm join --control-plane`

### kubeadm HA topology - stacked etcd

![Stacked Topology](./Resources/Stacked_Topology.png)

<br></br>

## External etcd topology

An HA cluster with external etcd is a topology where the distributed data storage cluster provided by etcd is external to the cluster formed by the nodes that run control plane components.

Like the stacked etcd topology, each control plane node in an external etcd topology runs an instance of the `kube-apiserver`, `kube-scheduler`, and `kube-controller-manager`. And the `kube-apiserver` is exposed to worker nodes using a load balancer. However, etcd members run on separate hosts, and each etcd host communicates with the `kube-apiserver` of each control plane node.

This topology decouples the control plane and etcd member. It therefore provides an HA setup where losing a control plane instance or an etcd member has less impact and does not affect the cluster redundancy as much as the stacked HA topology.

However, this topology requires twice the number of hosts as the stacked HA topology. A *minimum of three* hosts for control plane nodes and *three hosts* for etcd nodes are required for an HA cluster with this topology

### kubeadm HA topology - external etcd

![External Topology](./Resources/External_Topology.png)