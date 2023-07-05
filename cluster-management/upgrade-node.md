# Upgrade Node
Upgrade the Master by upgrade `kubelet`
## Tasks list
- [x] I. Cordon the Node
- [x] II. Drain the Node
- [x] III. Upgrade `kubelet` package
- [x] IV. Upgrade `kubectl` package
- [x] V. UnCordon the Node


> Check current version of `kubeadm`
```
kubeadm version -o short
```

> Check current version of `kubectl` `api & master components`
```
kubectl version --short
```

### I.  Cordon the Node

> Cordon the Node using `kubectl` command
```
kubectl cordon node k8s-node-01
```

### II.  Drain the Node

> Drain the Node using `kubectl` command

```
kubectl drain k8s-node-01
```

### III.  Upgrade `kubelet` package

> Upgrade `kubelet` package
```
apt-mark unhold kubelet
apt install -y kubeadm=1.27.3-00
apt-mark hold kubelet
```

### IV.  Upgrade `kubectl` package

> Upgrade `kubectl` package
```
apt-mark unhold kubectl
apt install -y kubeadm=1.27.3-00
apt-mark hold kubectl
```

### V.  UnCordon the Node

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
