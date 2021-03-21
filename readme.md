# Traefik v2 + cert-manager

## Create secret

```bash
kubectl apply -f cloudflare.yaml
```

## Install traefik v2

```bash
helm repo add traefik https://containous.github.io/traefik-helm-chart
helm repo update

kubectl create namespace traefik
helm install --namespace traefik traefik traefik/traefik --values traefik/values.yaml
```

## Access to the dashboard

```bash
kubectl port-forward -n traefik $(kubectl get pods -n traefik --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000

wget http://127.0.0.1:9000/dashboard/
```

## Install cert-manager

```bash
# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --version v1.2.0 \
  --set installCRDs=true
```

- Verifying the installation

```bash
kubectl get pods --namespace cert-manager
```

- Create cluster issuer

```bash
kubectl apply -f cert-manager/cluster-issuer.yaml
```
- Expose the traefik dashboard

```bash
kubectl apply -f traefik-dashboard-ingress.yaml
```

- Check the certificate issuer with the command:

```bash
echo | openssl s_client -showcerts -servername traefik.k8s.rsletten.com -connect traefik.k8s.rsletten.com:443 2>/dev/null | openssl x509 -inform pem -text
```
