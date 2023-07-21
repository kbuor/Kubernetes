# Deploy Master
Deploy new Kubernetes Cluster using `kubeadm`
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

--------------

# Deploy Node
Use Kubeadm to bootstraping a Kubernetes cluster

## Tasks list
- [x] I. Prepare linux
- [x] II. Install Dependancy
- [x] III. Join Node to Master

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
### III. Join Node to Master
> Run the join command. This command can be find by `kubeadm token create --print-join-command` command run from master.


-----------

# Deploy Kubernetes Dashboard
Deploy the dashboard for kubernetes
> Ref. Link: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
## Tasks list
- [x] I. Deploy Dashboard UI
- [x] II. Create SSL Certificates
- [x] III. Create user and get token

### I. Deploying the dashboard UI

> Download the dashboard.yaml manifest
```
wget https://raw.githubusercontent.com/kbuor/Kubernetes/main/manifest/dashboard.yaml
```

> Create the workload and services
```
kubectl apply -f dashboard.yaml
```

### II. Create `kubernetes-dashboard-certs`
> Using OpenSSL to generate SSL self-Certificate
```
mkdir certs
chmod -R 777 certs
openssl req -nodes -newkey rsa:2048 -keyout certs/dashboard.key -out certs/dashboard.csr -subj "/C=/ST=/L=/O=/OU=/CN=kubernetes-dashboard"
openssl x509 -req -sha256 -days 365 -in certs/dashboard.csr -signkey certs/dashboard.key -out certs/dashboard.crt
```

> Create secret for `kubernetes-dashboard-certs`
```
kubectl create secret generic kubernetes-dashboard-certs --from-file=certs -n kubernetes-dashboard
```

### III. Create User and Get Token to Login
> Create Service Account
```
wget https://raw.githubusercontent.com/kbuor/Kubernetes/main/manifest/service-account.yaml
kubectl apply -f service-account.yaml
```
> Create ClusterRoleBinding
```
wget https://raw.githubusercontent.com/kbuor/Kubernetes/main/manifest/cluster-role-binding.yaml
kubectl apply -f cluster-role-binding.yaml
```
> Get token
```
kubectl -n kubernetes-dashboard create token admin-user
```

> (Optional) Install K9s
```
curl -sS https://webinstall.dev/k9s | bash
```
