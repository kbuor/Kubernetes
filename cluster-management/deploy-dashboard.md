# Deploy Kubernetes Dashboard
Deploy the dashboard for kubernetes
> Ref. Link: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
## Tasks list
- [x] I. Prepare linux
- [x] II. Install Dependancy
- [x] III. Bootstraping Master using Kubeadm

### I. Deploying the dashboard UI

> Download the dashboard.yaml manifest
```
wget https://github.com/kbuor/Kubernetes/blob/main/manifest/dashboard.yaml
```

> Create the workload and services
```
kubectl apply -f dashboard.yaml
```
