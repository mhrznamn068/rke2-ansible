# Ansible Role: Rancher RKE2 Configuration

This Ansible role automates the installation and configuration of RKE2 (Rancher Kubernetes Engine 2) on your infrastructure. It handles all steps necessary to configure a fully functional RKE2 Kubernetes cluster.

## Requirements

- Ansible
- Target systems should be running a supported version of Linux (e.g., Ubuntu )
- User must have `sudo` privileges on the target hosts
- Internet access on target hosts (for downloading RKE2 binaries)

## Role Variables

### Mandatory Variables:

- `RKE2_VERSION`: Version of RKE2 to install (e.g., `v1.30.2+rke2r1`)
- `CLUSTER_NAME`: Name of the Kubernetes cluster
- `ENV`: Define the environment of the cluster `dev`, `prod`
- `TOKEN`: Shared token for authenticating RKE2 nodes
- `MASTER1_IP`: Master Node 1 IP Address
- `MASTER2_IP`: Master Node 2 IP Address
- `MASTER3_IP`: Master Node 3 IP Address
- `ROOT_DOMAIN`: Root Domain to add in TLS SAN of master node

### Optional Variables:

- `DIRECTORIES`: Configuration directory for RKE2 (default: `/etc/rancher/rke2`)
- `DISABLE_SWAP`: Additional flags to enable and disable swap
- `KUBECTL_VERSION`: Kubectl Version
### Example Configuration:
#### Install Role from Anisble Galaxy
```bash
ansible-galaxy role install mhrznamn068.rke2-ansible
```

#### Create playbook
```yaml
# Example playbook
---
- hosts: all
  become: true
  roles:
    - role: mhrznamn068.rke2-ansible
      vars:
        RKE2_VERSION: "v1.30.2+rke2r1"
        CLUSTER_NAME: "my-kubernetes-cluster"
        ENV: "prod"
        TOKEN: "your-shared-token"
        MASTER1_IP: "10.100.10.0"
        MASTER2_IP: "10.100.10.1"
        MASTER3_IP: "10.100.10.2"
        ROOT_DOMAIN: "example.com"
```

#### Create inventory
```bash
touch inventory
```
#### Inventory Content
```
[master]
master1 ansible_host=master1 NODE_ROLE=master INDEX=1
master2 ansible_host=master2 NODE_ROLE=master INDEX=2
master3 ansible_host=master3 NODE_ROLE=master INDEX=3

[worker]
worker1 ansible_host=worker1 NODE_ROLE=worker INDEX=1
worker2 ansible_host=worker2 NODE_ROLE=worker INDEX=2

[master:vars]
ansible_python_interpreter=/usr/bin/python3

[worker:vars]
ansible_python_interpreter=/usr/bin/python3
```
### Run the playbook
```bash
ansible-playbook -i inventory rke2-playbook.yml
```

### Verifying the Installation:
After running the playbook, you can verify the RKE2 installation by checking the status of the RKE2 services:

#### For master nodes:
```bash
sudo systemctl status rke2-server
```

#### For worker nodes:

```bash
sudo systemctl status rke2-agent
```

#### To interact with the cluster:

```bash
kubectl --kubeconfig /etc/rancher/rke2/rke2.yaml get nodes
```
