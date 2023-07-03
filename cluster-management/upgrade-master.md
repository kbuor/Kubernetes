# Upgrade Master
Upgrade the Master using `kubeadm`
## Tasks list
- [x] I. Upgrade `kubeadm` package
- [x] II. Check upgrade plan
- [x] III. Upgrade Master and components
- [x] IV. Upgrade `kubelet` package
- [x] V. Upgrade `kubectl` package

### I. Upgrade `kubeadm` package

> Upgrade `kubeadm` package
```
apt-mark unhole kubeadm
apt install -y kubeadm=1.27.3-00
apt-mark hole kubeadm
```

### II. Check upgrade plan

> Check upgrade plan using `kubeadm`: docker, kubeadm, kubelet

```
kubeadm upgrade plan
```

### III. Upgrade Master and Components

> Run the recommand command receive from upgrade plan

```
kubeadm apply plan
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
kubectl get pods --all-namespaces
```
- Show join command
```
kubeadm token create --print-join-command
```
