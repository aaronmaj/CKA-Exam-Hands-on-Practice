### Creating ClusterRoles and ClusterRoleBindings for Service Accounts
Create a ClusterRole from podcreator.yaml.
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: podcreator
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["create", "update", "patch"]
```
Apply the manifest.
```bash
kubectl apply -f podcreator.yaml
```
Create a ClusterRoleBinding for podcreator Service Account
```bash
kubectl create clusterrolebinding read-pod-global --clusterrole=podcreator --serviceaccount=default:podcreator
```
Run the following command to verify that the ClusterRoleBinding was created successfully.
```bash 
kubectl edit clusterrolebinding read-pod-global
```
The output should look like this:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-pod-global
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: podcreator
subjects:
- kind: ServiceAccount
  name: podcreator
  namespace: default
```