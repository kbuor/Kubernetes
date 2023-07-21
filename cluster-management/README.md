# Deploy Master
## Prepare Linux
- Update linux repository
```yaml
apt update -y
apt upgrade -y
```
- Disable Swap
```
swapoff -a
```
## Install Dependency

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
## Bootstraping Master
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

## Prepare Linux

- Update linux repository
```
apt update -y
apt upgrade -y
```
- Disable Swap
```
swapoff -a
```

## Install Dependency

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
## Join Node to Master
> Run the join command. This command can be find by `kubeadm token create --print-join-command` command run from master.


-----------

# Deploy Kubernetes Dashboard
Deploy the dashboard for kubernetes
> Ref. Link: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

## Deploying the dashboard UI

> Download the dashboard.yaml manifest
```
wget https://raw.githubusercontent.com/kbuor/Kubernetes/main/manifest/dashboard.yaml
```

> Create the workload and services
```
kubectl apply -f dashboard.yaml
```

## Create `kubernetes-dashboard-certs`
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

## Create User and Get Token to Login
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
-----------

# Upgrade Master

> Check current version of `kubeadm`
```
kubeadm version -o short
```

> Check current version of `kubectl` `api & master components`
```
kubectl version --short
```


## Upgrade `kubeadm` package

> Upgrade `kubeadm` package
```
apt-mark unhold kubeadm
apt install -y kubeadm=1.27.3-00
apt-mark hold kubeadm
```

## Check upgrade plan

> Check upgrade plan using `kubeadm`: docker, kubeadm, kubelet

```
kubeadm upgrade plan
```

## Upgrade Master and Components

> Run the recommand command receive from upgrade plan

```
kubeadm apply plan
```

## Upgrade `kubelet` package

> Upgrade `kubelet` package
```
apt-mark unhold kubelet
apt install -y kubeadm=1.27.3-00
apt-mark hold kubelet
```

## Upgrade `kubectl` package

> Upgrade `kubectl` package
```
apt-mark unhold kubectl
apt install -y kubeadm=1.27.3-00
apt-mark hold kubectl
```

> Check current version of `kubeadm`
```
kubeadm version -o short
```

> Check current version of `kubectl` `api & master components`
```
kubectl version --short
```
-----------
# Upgrade Node
Upgrade the Master by upgrade `kubelet`

> Check current version of `kubeadm`
```
kubeadm version -o short
```

> Check current version of `kubectl` `api & master components`
```
kubectl version --short
```

## Cordon the Node

> Cordon the Node using `kubectl` command
```
kubectl cordon node k8s-node-01
```

## Drain the Node

> Drain the Node using `kubectl` command

```
kubectl drain k8s-node-01
```

## Upgrade `kubelet` package

> Upgrade `kubelet` package
```
apt-mark unhold kubelet
apt install -y kubeadm=1.27.3-00
apt-mark hold kubelet
```

## Upgrade `kubectl` package

> Upgrade `kubectl` package
```
apt-mark unhold kubectl
apt install -y kubeadm=1.27.3-00
apt-mark hold kubectl
```

## UnCordon the Node

> UnCordon the Node using `kubectl` command
```
kubectl uncordon node k8s-node-01
```
> Check current version of `kubeadm`
```
kubeadm version -o short
```

> Check current version of `kubectl` `api & master components`
```
kubectl version --short
```
