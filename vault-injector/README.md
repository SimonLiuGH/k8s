# Vault

## Install Minikube

### Configure proxy

#### Configure environment variables

```bash
export HTTP_PROXY=http://<proxy-ip>:<proxy-port>
export HTTPS_PROXY=http://<proxy-ip>:<proxy-port>
export NO_PROXY=localhost,127.0.0.1,10.96.0.0/12,192.168.59.0/24,192.168.49.0/24,192.168.39.0/24
```

#### Configure proxy for dockerd (WSL 2 with systemd enabled)

cat /etc/systemd/system/docker.service.d/http-proxy.conf

```
[Service]
Environment="HTTP_PROXY=http://<proxy-ip>:<proxy-port>/"
Environment="HTTPS_PROXY=http://<proxy-ip>:<proxy-port>/"
Environment="NO_PROXY=localhost,127.0.0.0/8,docker-registry.somecorporation.com"
```

#### Configure config.json for Docker Daemon per user

cat ~/.docker/config.json

```
{
        "proxies": {
                "default": {
                        "httpProxy": "http://<proxy-ip>:<proxy-port>",
                        "httpsProxy": "https://<proxy-ip>:<proxy-port>",
                        "noProxy": "127.0.0.0/8"
                }
        }
}
```

### Install Minikube Cluster

```bash
curl -LO https://storage.googleapis.com/minikube/releases/v1.33.1/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
```

### Install kubectl CLI

```bash
curl -LO https://dl.k8s.io/release/v1.30.2/bin/linux/amd64/kubectl
#curl -LO https://dl.k8s.io/release/v1.30.2/bin/linux/amd64/kubectl --proxy http://<proxy-ip>:<proxy-port>
sudo install kubectl /usr/local/bin/kubectl
kubectl version --output=yaml --client
```

### Initialize and Start Minikube Cluster

```bash
minikube config set driver docker
minikube start --kubernetes-version=v1.30.0 --memory=8192 --cpus=2
#minikube start --kubernetes-version=v1.30.0 --memory=8192 --cpus=2 --docker-env HTTP_PROXY=http://<proxy-ip>:<proxy-port> --docker-env HTTPS_PROXY=http://<proxy-ip>:<proxy-port> --docker-env NO_PROXY=localhost,127.0.0.1,10.96.0.0/12,192.168.59.0/24,192.168.49.0/24,192.168.39.0/24

minikube addons enable metallb

minikube ip
minikube addons configure metallb

minikube addons enable kong
kubectl version --output=yaml

minikube dashboard
```

## Install Helm CLI

```bash
curl -LO https://get.helm.sh/helm-v3.15.2-linux-amd64.tar.gz
tar xvf helm-v3.15.2-linux-amd64.tar.gz

sudo install ./linux-amd64/helm /usr/local/bin/helm

helm version
helm repo add bitnami https://charts.bitnami.com/bitnami
helm search repo bitnami
```



## Install Vault


```bash
kubectl create ns vault
helm install vault hashicorp/vault --version 0.27.0 --set "server.dev.enabled=true" -n vault
```


kubectl port-forward svc/vault 8200:8200 -n vault

