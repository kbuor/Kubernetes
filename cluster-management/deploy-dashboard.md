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
> Create Service Account
```
wget https://github.com/kbuor/Kubernetes/blob/main/manifest/service-account.yaml
kubectl apply -f service-account.yaml
```
> Create ClusterRoleBinding
```
https://github.com/kbuor/Kubernetes/blob/main/manifest/cluster-role-binding.yaml
kubectl apply -f cluster-role-binding.yaml
```
> Get token
```
kubectl -n kubernetes-dashboard create token admin-user
```
