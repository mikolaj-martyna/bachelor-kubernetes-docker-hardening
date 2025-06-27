# Implementing security measures for Docker and Kubernetes-based environments using a local cluster

This repository contains Ansible playbooks and roles for hardening Docker and Kubernetes environments according to CIS benchmarks. The automation handles both standalone Docker hosts and complete Kubernetes clusters, making it easy to apply security best practices across your container infrastructure.

## What's inside

The project is organized into two main security domains:

- **Docker**: Secures Docker daemon configuration, host-level settings, and audit rules
- **Kubernetes**: Comprehensive security for K8s clusters including control plane components (API server, etcd, scheduler, controller manager) and worker nodes

## Quick start

### Prerequisites

- Ansible installed on your machine
- SSH access to target hosts
- Sudo privileges on target systems
- Working Docker/Kubernetes cluster

### Setup

1. Clone this repository
2. Configure your inventory by editing `inventory/credentials.yml`
3. Update host IPs and connection details
4. Run the appropriate playbook

### Testing connectivity

Before running any hardening playbooks, it's a good idea to test your connectivity:

```bash
ansible-playbook playbooks/docker/ping.yml

ansible-playbook playbooks/kubernetes/ping.yml
```

### Running the playbooks

**For Docker environments:**
```bash
ansible-playbook playbooks/docker/harden.yml
```

**For Kubernetes clusters:**
```bash
ansible-playbook playbooks/kubernetes/harden.yml
```

## Configuration

### Host inventory

The main configuration happens in `inventory/credentials.yml`. Here's what you can customize:

```yaml
# IP addresses of your hosts
control_plane_1_ip: 192.168.0.100
worker_1_ip: 192.168.0.101
docker_1_ip: 192.168.0.200

# SSH connection settings
ansible_user: vm
ssh_private_key: ~/.ssh/id_rsa
ansible_become_password: vm

# Encryption settings
encryption_key: some_encryption_key
```

### Host groups

The inventory defines several host groups in `inventory/hosts.yaml`:

- **`docker_hosts`**: Standalone Docker hosts that need hardening
- **`k8s_cluster`**: Kubernetes cluster nodes
- **Individual hosts**: specific node types

### Kubernetes node types

Each K8s host can have one or more roles:
- `control_plane`: Runs master components (API server, etcd, scheduler, controller manager)
- `worker`: Runs workload pods

Mix and match as needed - the same node can be both control plane and worker.

## What gets hardened

### Docker security
- Host OS configuration and kernel parameters
- Docker daemon security settings
- File permissions and ownership
- Audit logging configuration
- Network security settings

### Kubernetes security
- **Control Plane**: API server security, admission controllers, encryption at rest
- **etcd**: Secure communication, proper authentication
- **Scheduler & Controller Manager**: Security configurations
- **Worker Nodes**: kubelet security, proper RBAC
- **Logging**: Comprehensive audit logging setup

## Project Structure

```
├── playbooks/          # Main entry points
│   ├── docker/         # Docker-specific playbooks
│   └── kubernetes/     # Kubernetes-specific playbooks
├── roles/              # Ansible roles containing the hardening logic
│   ├── docker/         # Docker hardening tasks and templates
│   └── kubernetes/     # K8s hardening (control_plane + worker_nodes)
├── inventory/          # Host definitions and variables
└── ansible.cfg         # Ansible configuration
```

## Tips for usage

- **Start small**: Test on a development environment first
- **Backup**: Always backup your configurations before applying changes
- **Review**: Check the CIS benchmark documentation to understand what each control does
- **Customize**: Modify variables to fit your specific environment needs
- **Monitor**: Keep an eye on your applications after hardening - some security measures might affect functionality

## Contributing

Feel free to submit issues or pull requests if you find bugs or have suggestions for improvements. When contributing, please ensure your changes maintain compatibility with the CIS benchmark standards.

## Security notice

This automation implements security hardening measures that may impact system functionality. Always test in a non-production environment first and review the changes being made to understand their implications for your specific use case.
