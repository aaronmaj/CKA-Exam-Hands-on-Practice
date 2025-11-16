### Generate a private key and a CertificateSigningRequest (CSR) file for the user “Viola” using OpenSSL.
```bash
#Create an X.509 certificate signing request
openssl genrsa -out viola.key 2048
#Create a certificate signing request using the private key
openssl req -new -key viola.key -subj "/CN=viola/O=developers" -out viola.csr
```

### Submit the CSR object in Kubernetes and approve the request using kubectl.
```bash
#Create a Kubernetes CertificateSigningRequest  object
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: viola
spec:
  request: $(cat viola.csr | base64 | tr -d '\n')
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400
  usages:
  - client auth
EOF
#Check the CSR status
kubectl get csr
#Approve the CSR
kubectl certificate approve viola
#Check the CSR status again
kubectl get csr
```
### Add the credentials from the CSR to a new kubeconfig for Viola
```bash
#Get the certificate
kubectl get csr viola -o yaml
kubectl get csr viola -o jsonpath='{.status.certificate}' | base64 -d > viola.crt

#Add the credentials from the CSR to a new kubeconfig for Viola.
kubectl config set-credentials viola --client-key=viola.key --client-certificate=viola.crt --embed-certs=true
```
### Configure the context for the kubeconfig (Add the new user to the Kubernetes cluster). 
```bash
kubectl config set-context viola --cluster=kubernetes-admin@kubernetes --user=viola
```
### Test the user’s access by using the auth can-i kubectl command.
```bash
kubectl --context viola auth can-i create pods
kubectl auth can-i get pods -n default --as viola
```
### Create a role and role binding that will allow Viola to create, list, and delete pods and deployments.
```bash
kubectl create role developer --verb=create,get,list,delete --resource=pods,deployments
kubectl create rolebinding developer-binding --role=developer-role --user=viola --group=developers 
```
