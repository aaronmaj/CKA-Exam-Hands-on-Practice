### Using “auth can-I” to Verify Access to Resources
Run the following commands to see what users and service accounts have access to:
```bash
kubectl auth can-i create deployments --namespace=default --as=system:serviceaccount:default:default
kubectl auth can-i create pods --namespace=default --as=system:serviceaccount:default:podcreator
kubectl auth can-i watch pods --namespace=default --as=viola
kubectl auth can-i create pods --namespace=web --as=system:serviceaccount:default:podcreator
```