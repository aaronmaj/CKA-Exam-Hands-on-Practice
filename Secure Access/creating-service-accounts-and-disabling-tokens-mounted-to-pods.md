### Creating Service Accounts and Disabling Tokens Mounted to Pods
Apply the following manifest file to create a Service Account and disable the token mounted to the pod.
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: podcreator
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nomount-sa
automountServiceAccountToken: false
---
apiVersion: v1
kind: Pod
metadata:
  name: nginxpod
spec:
  containers:
    - image: nginx:latest
      name: nginxpod
  serviceAccountName: podcreator.yaml
  automountServiceAccountToken: false
```
Then execute the following commands to create the Service Account and check if the token is mounted to the pod or not.
```bash
# Apply the previous manifest file
kubectl apply -f service-accounts.yaml
kubectl get pod nginxpod | grep serviceAccountName 
# Check if the default Service Account is automatically mounted to the pod or not
kubectl get pod nginxpod -o yaml | grep volumeMounts -A14
```