# Deploy Kubernetes Dashboard
Deploy the dashboard for kubernetes
> Ref. Link: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
## Tasks list
- [x] I. Prepare linux
- [x] II. Install Dependancy
- [x] III. Bootstraping Master using Kubeadm

### I. Prepare the manifest for deployment

- Update linux repository
```
apt update -y
apt upgrade -y
```
- Disable Swap
```
swapoff -a
```

### II. Install Dependency

> Dependency need to install: docker, kubeadm, kubelet

- Add GPG key for Docker repository
```
apt-get install ca-certificates curl gnupg lsb-release
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
