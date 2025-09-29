# Ansible Learning Environment with Vagrant

This repository provides a complete Ansible learning environment using Vagrant and VirtualBox. It creates 4 virtual machines: 1 Ansible controller and 3 target nodes for practicing automation.

## 📋 Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Architecture](#architecture)
- [Quick Start](#quick-start)
- [Network Configuration](#network-configuration)
- [VM Specifications](#vm-specifications)
- [SSH Key Setup](#ssh-key-setup)
- [Ansible Configuration](#ansible-configuration)
- [Troubleshooting](#troubleshooting)
- [Learning Resources](#learning-resources)

## 🎯 Overview

This environment provides:
- **1 Ansible Controller**: Pre-configured with Ansible, Python, and SSH tools
- **3 Target Nodes**: Ready to be managed by Ansible
- **Network Isolation**: Private network for secure inter-VM communication
- **Internet Access**: All VMs can access external resources for package installation
- **SSH Authentication**: Automated SSH key generation and distribution

## 📋 Prerequisites

### Required Software
- [VirtualBox](https://www.virtualbox.org/) (6.1 or higher)
- [Vagrant](https://www.vagrantup.com/) (2.2 or higher)
- At least 4GB RAM available
- At least 10GB free disk space

### System Requirements
- **Host OS**: Windows, macOS, or Linux
- **Virtualization**: VT-x/AMD-V enabled in BIOS
- **Network**: Internet connection for initial setup

## 🏗️ Architecture

```
Host Machine (Windows/Mac/Linux)
├── ansible-controller (192.168.56.2)
│   ├── Ansible installed
│   ├── SSH keys generated
│   └── Management tools
├── ansible-node1 (192.168.56.3)
├── ansible-node2 (192.168.56.4)
└── ansible-node3 (192.168.56.5)
```

### Network Topology
- **NAT Network**: Internet access for all VMs
- **Private Network**: 192.168.56.0/24 subnet for inter-VM communication
- **Port Forwarding**: SSH access from host to VMs

## 🚀 Quick Start

### 1. Clone and Setup
```bash
git clone <your-repo-url>
cd ansible-learn
```

### 2. Start the Environment
```bash
# Start all VMs (this will take 10-15 minutes on first run)
vagrant up

# Check status
vagrant status
```

### 3. Access the Controller
```bash
# SSH into the Ansible controller
vagrant ssh ansible-controller

# Check Ansible installation
ansible --version
```

### 4. Setup SSH Keys
```bash
# Manually copy the id-rsa.pub from your ansible-controller
# to authorized-keys each ansible-nodes
```

### 5. Test Ansible Connectivity
```bash
# SSH into controller
vagrant ssh ansible-controller

# Test connection to all nodes
ansible all -i "192.168.56.3,192.168.56.4,192.168.56.5," -m ping
```

## 🌐 Network Configuration

### IP Address Allocation
| VM Name | Hostname | IP Address | SSH Port (Host) |
|---------|----------|------------|----------------|
| windows | windows | 192.168.56.10 | 22 |
| ansible-controller | ansible-controller | 192.168.56.2 | 2222 |
| ansible-node1 | ansible-node1 | 192.168.56.3 | 2221 |
| ansible-node2 | ansible-node2 | 192.168.56.4 | 2222 |
| ansible-node3 | ansible-node3 | 192.168.56.5 | 2223 |

### Network Features
- **Internet Access**: All VMs have NAT networking for package downloads
- **Inter-VM Communication**: Private network (192.168.56.0/24) for Ansible operations
- **DNS Resolution**: Configured with Google DNS (8.8.8.8, 8.8.4.4) and Cloudflare (1.1.1.1)
- **SSH Port Forwarding**: Direct access from host machine

## 💻 VM Specifications

### Ansible Controller
- **OS**: Debian 11 (Bullseye)
- **Memory**: 1024 MB
- **CPU**: 1 core
- **Software**: Ansible, Python3, SSH tools, Git, Vim

### Target Nodes (3x)
- **OS**: Debian 11 (Bullseye)
- **Memory**: 512 MB each
- **CPU**: 1 core each
- **Software**: Python3, SSH server

### Total Resource Usage
- **Memory**: 2.5 GB (1GB controller + 1.5GB nodes)
- **CPU**: 4 cores total
- **Disk**: ~8GB (2GB per VM)

## 🔐 SSH Key Setup

### Automatic SSH Key Generation
The controller VM automatically generates SSH keys during provisioning:
- **Location**: `/home/vagrant/.ssh/id_rsa`
- **Type**: RSA 4096-bit
- **User**: vagrant

### SSH Configuration
The controller has pre-configured SSH settings:
```bash
# /home/vagrant/.ssh/config
Host 192.168.56.*
    StrictHostKeyChecking no
    UserKnownHostsFile=/dev/null
```

## 📚 Ansible Configuration

### Pre-configured Directory Structure
```
/home/vagrant/ansible/
├── inventory/          # Inventory files
├── playbooks/          # Ansible playbooks
└── roles/             # Ansible roles
```

### Sample Inventory File
Create your inventory file:
```bash
# SSH into controller
vagrant ssh ansible-controller

# Create inventory
cat > /home/vagrant/ansible/inventory/hosts <<EOF
[nodes]
192.168.56.3
192.168.56.4
192.168.56.5

[nodes:vars]
ansible_user=vagrant
ansible_ssh_private_key_file=/home/vagrant/.ssh/id_rsa
EOF
```

### Basic Ansible Commands
```bash
# Test connectivity
ansible all -i /home/vagrant/ansible/inventory/hosts -m ping

# Run ad-hoc commands
ansible all -i /home/vagrant/ansible/inventory/hosts -m command -a "uptime"

# Gather facts
ansible all -i /home/vagrant/ansible/inventory/hosts -m setup
```

## 🔧 Vagrant Commands

### VM Management
```bash
# Start all VMs
vagrant up

# Start specific VM
vagrant up ansible-controller

# SSH into VMs
vagrant ssh ansible-controller
vagrant ssh ansible-node1

# Stop VMs
vagrant halt

# Restart VMs
vagrant reload

# Destroy VMs
vagrant destroy

# Check status
vagrant status

# Re-run provisioning
vagrant provision
```

### Troubleshooting Commands
```bash
# Check global status
vagrant global-status

# SSH config (for manual connections)
vagrant ssh-config

# Force halt unresponsive VM
vagrant halt --force

# Recreate VM
vagrant destroy ansible-node1
vagrant up ansible-node1
```

## 🐛 Troubleshooting

### Common Issues and Solutions

#### 1. DNS Resolution Failures
**Symptoms**: "Temporary failure resolving" errors during provisioning
**Solution**: The Vagrantfile includes DNS fixes, but if issues persist:
```bash
vagrant ssh ansible-controller
sudo echo "nameserver 8.8.8.8" > /etc/resolv.conf
sudo apt-get update
```

#### 2. SSH Connection Refused
**Symptoms**: Cannot SSH between VMs
**Solutions**:
```bash
# Check if nodes are running
vagrant status

# Restart SSH service on nodes
vagrant ssh ansible-node1 -c "sudo systemctl restart ssh"

# Re-run SSH setup script
vagrant ssh ansible-controller -c "bash /vagrant/setup-ssh-keys.sh"
```

#### 3. VirtualBox Network Issues
**Symptoms**: VMs can't reach internet or each other
**Solutions**:
```bash
# Restart VirtualBox host-only adapter
vagrant reload

# Check VirtualBox network settings
VBoxManage list hostonlyifs
```

#### 4. Out of Memory
**Symptoms**: VM startup fails with memory errors
**Solutions**:
- Close other applications
- Reduce VM memory in Vagrantfile
- Start VMs one by one: `vagrant up ansible-controller`

#### 5. Port Conflicts
**Symptoms**: "Port already in use" errors
**Solutions**:
```bash
# Check which process is using the port
netstat -tulpn | grep :2222

# Use auto-correction (already enabled in Vagrantfile)
vagrant up
```

### Log Analysis
```bash
# View Vagrant logs
vagrant up --debug

# Check VM logs in VirtualBox GUI
# VM Settings > System > Extended Features > Enable Serial Port
```

## 📖 Learning Resources

### Ansible Basics
Once your environment is running, try these exercises:

#### 1. Basic Connectivity Test
```bash
ansible all -i "192.168.56.3,192.168.56.4,192.168.56.5," -m ping
```

#### 2. System Information Gathering
```bash
ansible all -i "192.168.56.3,192.168.56.4,192.168.56.5," -m setup
```

#### 3. Package Management
```bash
ansible all -i "192.168.56.3,192.168.56.4,192.168.56.5," -m apt -a "name=htop state=present" --become
```

#### 4. File Operations
```bash
ansible all -i "192.168.56.3,192.168.56.4,192.168.56.5," -m copy -a "content='Hello Ansible' dest=/tmp/test.txt"
```

### Sample Playbook
Create your first playbook:
```yaml
# /home/vagrant/ansible/playbooks/hello.yml
---
- name: Hello World Playbook
  hosts: all
  tasks:
    - name: Create a test file
      copy:
        content: "Hello from Ansible on {{ inventory_hostname }}"
        dest: /tmp/hello.txt
    
    - name: Display system info
      debug:
        msg: "Running on {{ ansible_hostname }} with {{ ansible_processor_cores }} CPU cores"
```

Run the playbook:
```bash
ansible-playbook -i /home/vagrant/ansible/inventory/hosts /home/vagrant/ansible/playbooks/hello.yml
```

## 🔄 Environment Lifecycle

### Development Workflow
1. **Start Environment**: `vagrant up`
2. **Setup SSH**: Run SSH key script
3. **Develop/Test**: Create and run playbooks
4. **Reset if needed**: `vagrant destroy && vagrant up`
5. **Stop when done**: `vagrant halt`

### Best Practices
- Always test playbooks in this environment before production
- Use version control for your playbooks and inventory
- Take VirtualBox snapshots before major changes
- Keep the environment updated: `vagrant box update`

## 📝 Notes

- **Default User**: All VMs use `vagrant` user with sudo privileges
- **SSH Password**: Default password is `vagrant` (for initial setup only)
- **Shared Folder**: `/vagrant` directory is synced with your host project folder
- **Time Zone**: VMs use UTC time zone by default
- **Firewall**: Debian firewall is disabled by default for learning purposes

## 🤝 Contributing

To improve this environment:
1. Fork the repository
2. Make your changes
3. Test with `vagrant up`
4. Submit a pull request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

**Happy Learning with Ansible! 🚀**

For questions or issues, please check the [troubleshooting section](#troubleshooting) or open an issue in the repository.