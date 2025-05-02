# Azure Jumpbox SSH Cheat Sheet

## 1. Generate a New SSH Key Pair

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/new_jumpbox_key
````

## 2. Upload Public Key to Jumpbox VM

```bash
az vm user update \
  --resource-group aks-private-rg \
  --name aks-jumpbox \
  --username azureuser \
  --ssh-key-value ~/.ssh/new_jumpbox_key.pub
```

Grants SSH access for `azureuser` using your new key on the VM `aks-jumpbox`.

## 3. Connect to the Jumpbox

```bash
ssh -i ~/.ssh/new_jumpbox_key azureuser@20.217.216.36 -o StrictHostKeyChecking=no
```

Securely connects to the jumpbox using your private key.

## 4. Get ACR Credentials

```bash
az acr credential show --name nextjsbasicappprivate
```

Sure! Here's the minimal cheat sheet entry:

---

## 5. Connect to AKS Cluster

```bash
az aks get-credentials --resource-group aks-private-rg --name myaksprivatecluster --overwrite-existing
```

---

# Azure Cloud Shell Cheat Sheet – Public AKS

## 1. Connect to AKS Cluster

```bash
az aks get-credentials --resource-group my-aks-rg --name myakscluster
```

Adds cluster credentials to your kubeconfig file so you can run `kubectl` commands.

## 2. View Running Pods

```bash
kubectl get pods
```

Lists all pods in the current namespace.

## 3. Check Ingress Resources

```bash
kubectl get ingress
```

Shows ingress configurations for routing external traffic.

## 4. Test HTTPS Endpoint (Strict SSL)

```bash
curl https://4.156.50.243
```

Sends a secure request. Fails if the certificate is untrusted.

## 5. Test HTTPS Endpoint (Ignore SSL Errors)

```bash
curl https://4.156.50.243 -k
```

Bypasses SSL certificate verification (not secure, use only for testing).

## 6. Get ACR Credentials

```bash
az acr credential show --name nextjsbasicapp
```

Displays the login server username and passwords for the ACR registry `nextjsbasicapp`.

