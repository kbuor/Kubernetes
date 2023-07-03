# Upgrade Master
Upgrade the Master using `kubeadm`
## Tasks list
- [x] I. Upgrade `kubeadm` package
- [x] II. Check upgrade plan
- [x] III. Upgrade Master and components
- [x] IV. Upgrade `kubelet` package
- [x] V. Upgrade `kubectl` package

### I.  Upgrade `kubeadm` package

> Upgrade `kubeadm` package
```
apt-mark unhole kubeadm
apt install -y kubeadm=1.27.3-00
apt-mark hole kubeadm
```

### II.  Check upgrade plan

> Check upgrade plan using `kubeadm`: docker, kubeadm, kubelet

```
kubeadm upgrade plan
```

### III.  Upgrade Master and Components

> Run the recommand command receive from upgrade plan

```
kubeadm apply plan
```

### IV.  Upgrade `kubelet` package

> Upgrade `kubelet` package
```
apt-mark unhole kubelet
apt install -y kubeadm=1.27.3-00
apt-mark hole kubelet
```

### IV.  Upgrade `kubectl` package

> Upgrade `kubectl` package
```
apt-mark unhole kubectl
apt install -y kubeadm=1.27.3-00
apt-mark hole kubectl
```
