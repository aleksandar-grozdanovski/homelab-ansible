# Homelab Ansible - Infrastructure as Code

Ansible playbooks to configure and maintain my homelab server from scratch.

## 🎯 Goal

Rebuild the entire dellbox server in **under 30 minutes** from a fresh Debian installation.

## 📦 What This Automates

- Base system configuration (hostname, timezone, packages, user account with sudo)
- Docker + Docker Compose installation
- K3s Kubernetes installation (kubectl, helm)
- Security hardening: UFW firewall rules, SSH configuration

## 🚀 Quick Start

### Prerequisites
- Fresh Debian 12 installation on target server
- SSH access with password authentication (will be hardened)
- Ansible installed on control machine:
  ```bash
  sudo apt install ansible  # Debian/Ubuntu
  brew install ansible      # macOS
  ```
- Required Ansible collections:
  ```bash
  ansible-galaxy collection install -r requirements.yml
  ```

### Run Full Setup
```bash
# Configure your inventory
cp inventory/hosts.example inventory/hosts
nano inventory/hosts  # Edit with your server IP

# Run complete playbook
ansible-playbook -i inventory/hosts playbooks/site.yml -K

# -K prompts for sudo password on remote server
```

### Run Specific Playbooks
```bash
# Gather information only (no changes)
ansible-playbook -i inventory/hosts playbooks/info.yml -K

# Base system only
ansible-playbook -i inventory/hosts playbooks/base.yml -K

# Docker only
ansible-playbook -i inventory/hosts playbooks/docker.yml -K

# K3s only
ansible-playbook -i inventory/hosts playbooks/k3s.yml -K

# Security hardening only
ansible-playbook -i inventory/hosts playbooks/security.yml -K
```

## 📁 Repository Structure

```
homelab-ansible/
├── README.md                   # This file
├── ansible.cfg                 # Ansible configuration
├── requirements.yml            # Required Ansible collections
├── inventory/
│   ├── hosts                   # Server inventory (gitignored)
│   └── hosts.example           # Template
└── playbooks/
    ├── site.yml                # Main playbook (runs all)
    ├── info.yml                # Gather server facts (read-only)
    ├── base.yml                # Base system configuration
    ├── docker.yml              # Docker installation
    ├── k3s.yml                 # Kubernetes (K3s) setup
    └── security.yml            # UFW firewall, SSH hardening
```

## 🏗️ Current Server State (dellbox)

**Hardware**: Dell Vostro 3350, 6GB RAM, i7-2620M, 232GB SSD
**OS**: Debian 12 (Bookworm)
**IP**: 192.168.50.67 (static DHCP reservation)

**Installed**:
- Docker 27.x + Docker Compose v2
- K3s v1.34.3 (single-node Kubernetes)
- Pi-hole v6 (native, installed manually — playbook on the roadmap)
- UFW firewall (allow: 22/SSH, 53/DNS, 80/HTTP)
- Caddy reverse proxy (Docker)
- Flux CD v2.7.5 (GitOps — see [homelab-gitops](https://github.com/aleksandar-grozdanovski/homelab-gitops))
- Prometheus + Grafana (monitoring)

## 🔐 Security Notes

- **Secrets Management**: sensitive values (passwords, API keys) go in Ansible Vault, never in plain YAML
- **Inventory**: `inventory/hosts` is gitignored — never commit server IPs or credentials

### Using Ansible Vault
```bash
# Create encrypted variables file
ansible-vault create group_vars/secrets.yml

# Edit encrypted file
ansible-vault edit group_vars/secrets.yml

# Run playbook with vault password
ansible-playbook -i inventory/hosts playbooks/site.yml -K --ask-vault-pass
```

## 📚 Playbook Details

### base.yml
- Set hostname (`dellbox`)
- Configure timezone
- Install essential packages: vim, curl, git, htop, etc.
- Create user account with sudo privileges
- Disable lid-close suspend (for laptop servers)

### docker.yml
- Add Docker APT repository
- Install Docker Engine + CLI + Compose
- Add user to `docker` group
- Start and enable Docker service
- Test with `docker run hello-world`

### k3s.yml
- Download and install K3s
- Configure kubeconfig permissions
- Install kubectl, helm
- Test cluster access

### security.yml
- Configure UFW firewall rules
- SSH hardening (disable password auth, add keys)
- Optional: fail2ban installation
- Optional: WireGuard VPN

## 🧭 Why Infrastructure as Code

- **Reproducibility**: rebuild from scratch anytime, identically
- **Living documentation**: the playbooks *are* the server's documentation
- **Version control**: every infrastructure change is a tracked commit
- **Disaster recovery**: fast, predictable recovery from failures
- **Scaling**: the same playbooks can provision additional servers

## 🚧 Roadmap

- [x] Base system configuration playbook
- [x] Docker installation playbook
- [x] K3s installation playbook
- [x] Security hardening playbook
- [ ] Flux CD bootstrap playbook
- [ ] Pi-hole installation playbook
- [ ] Caddy reverse proxy playbook
- [ ] Backup automation playbook
- [ ] CI/CD pipeline to test playbooks

## 📝 License

MIT - Feel free to use for your own homelab!
