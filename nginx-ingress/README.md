> Setup NGINX ingress by Helm
```shell
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace
```

> Check Ingress Controller running
```shell
kubectl get pods -n ingress-nginx
```

> Create Deployment (`app.yaml`)
```shell
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
      - name: demo
        image: hashicorp/http-echo:0.2.3
        args:
        - "-text=Hello from Demo App"
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: demo-service
spec:
  selector:
    app: demo
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5678
  type: ClusterIP
```
