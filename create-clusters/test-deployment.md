# End-to-end Deployment Testing

- Deploy nginx

```
kubectl apply -f https://k8s.io/examples/application/deployment.yaml
```

- Verify pod can run

```
kubectl get pods
kubectl get deployments
```

- Verify pods can access directly

```
kubectl port-forward pods/<pod-id> 8081:80
curl --head http://localhost:8081
```

- View logs

```
kubectl logs -f pod-id
```

- Service can access

```
kubectl expose deployment nginx-deployment --type NodePort --port 80
kubectl get services
```

- Check access

```
curl --head http://localhost:nodeport
```

- Get node status

```
kubectl get nodes
```

- Get detail info pods

```
kubectl describe pods
```
