# Container runtimes

To run containers in Pods, Kubernetes uses a container runtime.

Here is an installation instruction for [Docker](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker) runtime.

## Docker

On each of your machines, install Docker. Version 19.03.4 is recommended, but 1.13.1, 17.03, 17.06, 17.09, 18.06, and 18.09 are known to work as well. Keep track of the latest verified Docker version in the Kubernetes release notes.

Use the floowing commands to install Docker on your Ubuntu 16.04+ system:

```sh
# Install Docker CE
## Set up the repository:
### Install packages to allow apt to use a repository over HTTPS
apt-get update && apt-get install \
  apt-transport-https ca-certificates curl software-properties-common

### Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -

### Add Docker apt repository.
add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) \
  stable"

## Install Docker CE.
apt-get update && apt-get install \
  containerd.io=1.2.10-3 \
  docker-ce=5:19.03.4~3-0~ubuntu-$(lsb_release -cs) \
  docker-ce-cli=5:19.03.4~3-0~ubuntu-$(lsb_release -cs)

# Setup daemon.
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

mkdir -p /etc/systemd/system/docker.service.d

# Restart docker.
systemctl daemon-reload
systemctl restart docker
```
