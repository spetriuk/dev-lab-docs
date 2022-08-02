# Infrastructure provisioning

The base of the infrastructure of the dev-lab is Docker and Kubernetes (k3s). The installation of docker and k3s is done via Ansible.

## Docker

**Docker** is an open platform to create, deploy and manage virtualized application containers on a common operating system.

**docker-compose** is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application's services.

**Installation**: [Ansible playbook]({{repo.url}}/insfastructure/docker)


## k3s

K3s is a highly available, certified Kubernetes distribution designed for production workloads in unattended, resource-constrained, remote locations or inside IoT appliances.

K3s is packaged as a single <50MB binary that reduces the dependencies and steps needed to install, run and auto-update a production Kubernetes cluster.

K3s works great from something as small as a Raspberry Pi to an AWS a1.4xlarge 32GiB server.

**[Project Page](https://k3s.io/)**

**Installation**: [Ansible playbook]({{repo.url}}/insfastructure/k3s)

When the setup steps for all nodes is complete, you’re ready to access the cluster remotely:

- SSH into the master node and copy the contents of `/etc/rancher/k3s/k3s.yaml` to `~/.kube/config` on your local machine.
- Replace the server: key’s IP address(127.0.0.1) with your master node’s public IP address.
- You can leave the port 6443 as is.
- Now you can access the cluster using `kubectl` or `Lens`
