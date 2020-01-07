# Installing Minikube in MacOS

## Before you begin

Check if virtualization is supported on macOS with the following commands:

```bash
sysctl -a | grep -E --color 'machdep.cpu.features|VMX' 
```

Make sure you see `VMX` in the output(should be colored)

## Installing Minikube

### Install `kubectl`

```bash
brew install kubectl

kubectl version
```

### Install a Hypervisor

`virtualbox` is highly recommended

### Install `minikube`

Easiest way to install Minikube on macOS is using homebrew:

```bash
brew install minikube
```

## Confirm Installation

To confirm successful installation of both hypervisor and minikube, run the following command to start up a local Kubernetes cluster:

```bash
minikube start --vm-driver=virtualbox
```

Once `minikube start` finishes, run the command below to check the status of the cluster:

```bash
minikube status

# expected output
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

After you have confirmed whether Minikube is working with your chosen hypervisor, you can continue to use Minikube or stop your cluster:

```bash
minikube stop
```

## Clean up local state

If your `minikube start` returns an error, run the following command to clean up your local state:

```bash
minikube delete
```
