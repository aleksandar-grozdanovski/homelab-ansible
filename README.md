# Homelab Ansible - Infrastructure as Code

Ansible playbooks to configure and maintain the homelab server from scratch.

## 🎯 Goal

Rebuild the entire dellbox server in **under 30 minutes** from a fresh Debian installation.

## 📦 What This Automates

- Base system configuration (hostname, timezone, packages)
- User account setup with sudo privileges
- UFW firewall rules
- Docker + Docker Compose installation
- K3s Kubernetes installation
- Pi-hole DNS server setup
- SSH hardening and key management

## 🚀 Quick Start

### Prerequisites
- Fresh Debian 12 installation on target server
- SSH access with password authentication (will be hardened)
- Ansible installed on control machine:
  ```bash
  sudo apt install ansible  # Debian/Ubuntu
  brew install ansible      # macOS
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
# Base system only
ansible-playbook -i inventory/hosts playbooks/base.yml -K

# Docker only
ansible-playbook -i inventory/hosts playbooks/docker.yml -K

# K3s only
ansible-playbook -i inventory/hosts playbooks/k3s.yml -K
```

## 📁 Repository Structure

```
homelab-ansible/
├── README.md                   # This file
├── ansible.cfg                 # Ansible configuration
├── inventory/
│   ├── hosts                   # Server inventory (gitignored)
│   └── hosts.example           # Template
├── playbooks/
│   ├── site.yml                # Main playbook (runs all)
│   ├── base.yml                # Base system configuration
│   ├── docker.yml              # Docker installation
│   ├── k3s.yml                 # Kubernetes (K3s) setup
│   ├── pihole.yml              # Pi-hole DNS server
│   └── security.yml            # SSH hardening, firewall
├── roles/
│   ├── common/                 # Base packages, users
│   ├── firewall/               # UFW configuration
│   ├── docker/                 # Docker + Compose
│   ├── k3s/                    # K3s installation
│   └── pihole/                 # Pi-hole setup
├── group_vars/
│   └── all.yml                 # Global variables
└── files/
    ├── caddyfile.j2            # Caddy reverse proxy template
    └── pihole-config.toml.j2   # Pi-hole configuration template
```

## 🏗️ Current Server State (dellbox)

**Hardware**: Dell Vostro 3350, 6GB RAM, i7-2620M, 232GB SSD  
**OS**: Debian 12 (Bookworm)  
**IP**: 192.168.50.67 (static DHCP reservation)

**Installed**:
- Docker 27.x + Docker Compose v2
- K3s v1.34.3 (single-node Kubernetes)
- Pi-hole v6 (native, not containerized)
- UFW firewall (allow: 22/SSH, 53/DNS, 80/HTTP)
- Caddy reverse proxy (Docker)
- Flux CD v2.7.5 (GitOps)
- Prometheus + Grafana (monitoring)

**DNS Records** (in Pi-hole):
- api.home.arpa
- pihole.home.arpa
- status.home.arpa
- portainer.home.arpa
- grafana.home.arpa
- vault.home.arpa

## 🔐 Security Notes

- **Secrets Management**: Sensitive values (passwords, API keys) should use Ansible Vault
- **SSH Keys**: Public keys stored in `files/ssh_authorized_keys`
- **Inventory**: `inventory/hosts` is gitignored - never commit server IPs/credentials

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
- Configure timezone (Europe/Berlin or your preference)
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

### pihole.yml
- Install Pi-hole (native or container)
- Configure DNS settings
- Add custom DNS records
- Set web admin password

### security.yml
- Configure UFW firewall rules
- SSH hardening (disable password auth, add keys)
- Optional: fail2ban installation
- Optional: WireGuard VPN

## 🎯 Interview Talking Points

**"How did you implement Infrastructure as Code?"**

*"I used Ansible to codify my entire homelab configuration. My playbooks can rebuild the server from a fresh Debian installation in under 30 minutes. This includes Docker, Kubernetes, networking, and all services. Everything is version-controlled in Git, so I have full traceability of infrastructure changes. I can also use the same playbooks to provision multiple servers with consistent configuration."*

**Key Benefits**:
- **Reproducibility**: Rebuild from scratch anytime
- **Documentation**: Code is living documentation
- **Version Control**: Track infrastructure changes in Git
- **Disaster Recovery**: Fast recovery from failures
- **Scaling**: Apply same config to multiple servers

## 📖 Resources

- [Ansible Documentation](https://docs.ansible.com/)
- [Ansible Best Practices](https://docs.ansible.com/ansible/latest/tips_tricks/ansible_tips_tricks.html)
- [Ansible Galaxy](https://galaxy.ansible.com/) - Community roles

## 🚧 Roadmap

- [ ] Base system configuration playbook
- [ ] Docker installation playbook
- [ ] K3s installation playbook
- [ ] Flux CD bootstrap playbook
- [ ] Pi-hole installation playbook
- [ ] Caddy reverse proxy playbook
- [ ] Backup automation playbook
- [ ] Terraform integration for cloud VMs
- [ ] CI/CD pipeline to test playbooks

## 📝 License

MIT - Feel free to use for your own homelab!

---

**Created**: January 13, 2026  
**Purpose**: DevOps/Platform Engineer portfolio project for Berlin tech market  
**Target**: €75-100k Platform Engineer roles
