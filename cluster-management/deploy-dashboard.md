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