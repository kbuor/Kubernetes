# Deploy Master
Use Kubeadm to bootstraping a Kubernetes cluster

## Tasks list
- [x] I. Prepare linux
- [x] II. Install Dependancy
- [x] III. Bootstraping Master using Kubeadm

### I. Prepare Linux

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
- Add Docker repository
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- Add GPG key for Kubernetes repository
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
```
- Add Kubernetes repository
```
cat << EOF | tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
```
- Update repository
```
apt update -y
```
- Install dependency components
```
apt install -y docker.io kubelet kubeadm kubectl
```
- Hold Kubernetes components version
```
apt-mark hold docker kubelet kubeadm
```
- Enable `kubelet` service
```
systemctl enable kubelet
```
### III. Bootstraping Master
- Bootstraping master using `kubeadm`
```
kubeadm init --pod-network-cidr=192.168.0.0/16
```
- Configure token
```
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/confi
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
- Apply overlay networking
```
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/custom-resources.yaml
```
## Optional command
- Get node information
```
kubectl get nodes
```
- Get node health
```
kubectl get componentstatuses
```
- Get system pods status
```
kubectl get pods --all-namespace
```
- Show join command
```
kubeadm token create --print-join-command
```
