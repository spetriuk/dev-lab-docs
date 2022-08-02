# Longhorn installation on k3s

Longhorn is a highly available persistent storage for Kubernetes

Project page: [https://longhorn.io](https://longhorn.io)

### Requirements
- k3s cluster
- kubectl
- helm
- Longhorn installed on cluster

### Installation

```bash
helm repo add longhorn https://charts.longhorn.io
```

```bash
helm repo update
```

```bash
helm install longhorn longhorn/longhorn --namespace longhorn --create-namespace
```

Make Longhorn a default storageclass:

```bash
kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

> Restarting a node causes the local-path storage class to be re-registered as the default. 
> See [this issue](https://github.com/k3s-io/k3s/issues/3441). 
> Consider disabling it entirely, or just be aware that occasionally you’ll get _Internal error occurred: 
> 2 default StorageClasses were found errors_, and you’ll have to patch it again. 

Create ingress for Longhorn UI:

```bash
kubectl apply -f ingress.yaml --namespace longhorn
```