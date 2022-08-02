# Gitea installation on k3s

Gitea is a painless self-hosted Git service. It is similar to GitHub, Bitbucket, and GitLab.

Project page: [https://gitea.io](https://gitea.io/en-us/)

### Requirements 
 - k3s cluster
 - kubectl
 - helm
 - Longhorn installed on cluster

### Installation

#### Create a namespace for gitea and related stuff
```bash
kubectl create namespace git
```

#### Install postgres
```bash
kubectl apply -f postgres
```

- The password for the postgres user is specified by the `POSTGRESS_PASSWORD` environment variable, which uses a secret ([postgres/secret.yaml]({{repo.url}}/service/gitea/postgres/secret.yaml))
- The data volume uses a `PersistentVolumeClaim` backed by Longhorn, so that it doesn't get deleted when the pod is restarted ([postgres/statefulset.yaml]({{repo.url}}/service/gitea/postgres/statefulset.yaml))
- Database is created by using initialization scripts in a ConfigMap. ([postgres/configmap.yaml]({{repo.url}}/service/gitea/postgres/configmap.yaml))
- The postgres instance needs to be exposed to the Gitea instance; use a ClusterIP service for that ([postgres/service.yaml]({{repo.url}}/service/gitea/postgres/service.yaml))

#### Install Gitea using Helm

[**Gitea Helm Chart**](https://gitea.com/gitea/helm-chart/)

```bash
helm repo add gitea-charts https://dl.gitea.io/charts/
```

```bash
helm repo update
```

```bash
helm install gitea gitea-charts/gitea --namespace git --values values.yaml
```

#### Expose Gitea installation

The DNS record pointing to cluster IP should be created, the host should be set to [ingress.yaml]({{repo.url}}/service/gitea/ingress.yaml) (spec.rules.host)


Expose http UI using Ingress service:
```bash
kubectl apply -f ingress.yaml
```
