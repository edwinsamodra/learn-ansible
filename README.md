# Learn Ansible - Complete Tutorial Environment

This repository provides a comprehensive Ansible learning environment with practical examples, playbooks, and tutorials. The project is designed to work seamlessly with Vagrant, where this entire folder is mounted to `/vagrant/` inside the Ansible controller VM, allowing you to run playbooks directly from the VM.

## 📋 Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Architecture](#architecture)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [How to Use](#how-to-use)
- [Available Playbooks](#available-playbooks)
- [Running Examples](#running-examples)
- [Vagrant Integration](#vagrant-integration)
- [Troubleshooting](#troubleshooting)
- [Learning Path](#learning-path)

## 🎯 Overview

This is a hands-on Ansible learning repository with practical examples and tutorials. The project includes:

### 🏗️ Infrastructure Setup
- **Vagrant Environment**: 4 VMs (1 controller + 3 nodes) with pre-configured networking
- **Ansible Controller**: Pre-installed Ansible with all necessary tools
- **Target Nodes**: Ready-to-manage Ubuntu servers

### 📚 Learning Materials
- **Real-world Playbooks**: From basic tasks to complex three-tier applications
- **Step-by-step Examples**: Progressive difficulty from beginner to advanced
- **Best Practices**: Production-ready Ansible code patterns
- **Documentation**: Comprehensive guides and explanations

### 🔧 Key Features
- **Shared Folder**: This entire project is mounted at `/vagrant/` in the controller VM
- **Ready-to-Run**: All playbooks are immediately executable
- **Multiple Scenarios**: Web servers, databases, application deployment, and more
- **Error Handling**: Robust playbooks with proper error management

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
cd learn-ansible
```

### 2. Start Vagrant Environment
```bash
# Start all VMs (takes 10-15 minutes on first run)
vagrant up

# Check VM status
vagrant status
```

### 3. Access the Ansible Controller
```bash
# SSH into the controller VM
vagrant ssh ansible-controller

# You're now inside the VM with this project mounted at /vagrant/
cd /vagrant
ls -la  # You'll see all the playbooks and inventory files
```

### 4. Test Your First Playbook
```bash
# Inside the controller VM (/vagrant directory)
ansible-playbook -i ansible/inventory/hosts ansible/playbooks/first_playbook.yml
```

## 📁 Project Structure

```
learn-ansible/
├── README.md                          # This file
├── Vagrantfile                        # VM configuration
├── ansible/
│   ├── inventory/
│   │   ├── hosts                      # Main inventory file
│   │   ├── crew.yml                   # Group-based inventory
│   │   └── example1.inventory.yml     # Example inventory formats
│   ├── playbooks/
│   │   ├── first_playbook.yml         # Basic Ansible tasks
│   │   └── call_vars.yml              # Variable usage examples
│   ├── roles/                         # Ansible roles (empty, for advanced use)
│   ├── three-tier-app/
│   │   ├── hosts.yml                  # Inventory for 3-tier app
│   │   └── three-tier-app.yml         # Complete web application deployment
│   └── test-connection/
│       ├── hosts.yml                  # Inventory for connectivity tests
│       └── test-connection.yml        # Internet connectivity tests
└── USAGE.md                           # This detailed guide
```

## 🔧 How to Use

### Step 1: Access the Controller
```bash
# From your host machine
vagrant ssh ansible-controller

# Navigate to the shared project folder
cd /vagrant
```

### Step 2: Understand the Folder Structure
- **This entire folder** is mounted as `/vagrant/` inside the controller VM
- All playbooks, inventory files, and examples are immediately available
- Changes made on your host machine are instantly reflected in the VM

### Step 3: Run Your First Playbook
```bash
# Basic connectivity test
ansible all -i ansible/inventory/hosts -m ping

# Run the first tutorial playbook
ansible-playbook -i ansible/inventory/hosts ansible/playbooks/first_playbook.yml
```

## 📚 Available Playbooks

### 1. **Basic Examples** (`ansible/playbooks/`)
- `first_playbook.yml`: Basic tasks (install packages, create users, copy files)
- `call_vars.yml`: Working with variables and facts

### 2. **Three-Tier Web Application** (`ansible/three-tier-app/`)
- **Frontend**: Nginx web server with HTML interface
- **Backend**: Node.js API server with Express.js
- **Database**: MariaDB with sample data
- **Features**: Complete CRUD application with REST API

### 3. **Network Testing** (`ansible/test-connection/`)
- Ping tests to various internet hosts
- DNS resolution verification
- HTTP/HTTPS connectivity checks
- Port accessibility tests

## 🎯 Running Examples

### Example 1: Test Internet Connectivity
```bash
cd /vagrant
ansible-playbook -i ansible/test-connection/hosts.yml ansible/test-connection/test-connection.yml
```

### Example 2: Deploy Three-Tier Application
```bash
cd /vagrant
ansible-playbook -i ansible/three-tier-app/hosts.yml ansible/three-tier-app/three-tier-app.yml
```

### Example 3: Basic System Management
```bash
cd /vagrant
ansible-playbook -i ansible/inventory/hosts ansible/playbooks/first_playbook.yml
```

### Example 4: Run on Specific Hosts
```bash
# Target only the database server
ansible-playbook -i ansible/three-tier-app/hosts.yml ansible/three-tier-app/three-tier-app.yml --limit db

# Target multiple specific hosts
ansible-playbook -i ansible/inventory/hosts ansible/playbooks/first_playbook.yml --limit "node1,node2"
```

## 🌐 VM Network Configuration

### IP Address Allocation
| VM Name | IP Address | Role | Access |
|---------|------------|------|--------|
| ansible-controller | 192.168.56.2 | Control Node | `vagrant ssh ansible-controller` |
| ansible-node1 | 192.168.56.3 | Frontend (fe) | Managed by Ansible |
| ansible-node2 | 192.168.56.4 | Backend (be) | Managed by Ansible |
| ansible-node3 | 192.168.56.5 | Database (db) | Managed by Ansible |

## � Vagrant Integration

### Key Integration Features

#### Shared Folder Mount
- **Host Path**: `./` (this entire project directory)
- **VM Path**: `/vagrant/`
- **Sync Type**: Real-time bidirectional sync
- **Benefits**: Edit files on host, run immediately in VM

#### Workflow Benefits
```bash
# On your host machine (Windows/Mac/Linux)
# Edit playbooks with your favorite IDE
code ansible/playbooks/first_playbook.yml

# Switch to VM to execute
vagrant ssh ansible-controller
cd /vagrant
ansible-playbook -i ansible/inventory/hosts ansible/playbooks/first_playbook.yml
```

### Vagrant Commands Cheat Sheet
```bash
# Start all VMs
vagrant up

# Start specific VM
vagrant up ansible-controller

# SSH into controller
vagrant ssh ansible-controller

# Check VM status
vagrant status

# Stop all VMs
vagrant halt

# Restart VMs
vagrant reload

# Destroy and recreate
vagrant destroy
vagrant up
```

## 📈 Learning Path

### 🚀 Beginner (Start Here)
1. **Get familiar with the environment**
   ```bash
   vagrant ssh ansible-controller
   cd /vagrant
   ansible --version
   ```

2. **Test basic connectivity**
   ```bash
   ansible all -i ansible/inventory/hosts -m ping
   ```

3. **Run your first playbook**
   ```bash
   ansible-playbook -i ansible/inventory/hosts ansible/playbooks/first_playbook.yml
   ```

### 🎯 Intermediate
1. **Explore variables and facts**
   ```bash
   ansible-playbook -i ansible/inventory/hosts ansible/playbooks/call_vars.yml
   ```

2. **Test network connectivity**
   ```bash
   ansible-playbook -i ansible/test-connection/hosts.yml ansible/test-connection/test-connection.yml
   ```

3. **Understand inventory management**
   - Study `ansible/inventory/hosts`
   - Compare with `ansible/inventory/crew.yml`

### 🏆 Advanced
1. **Deploy complete applications**
   ```bash
   ansible-playbook -i ansible/three-tier-app/hosts.yml ansible/three-tier-app/three-tier-app.yml
   ```

2. **Create your own playbooks**
   - Add new playbooks to `ansible/playbooks/`
   - Practice with roles in `ansible/roles/`

3. **Customize and extend**
   - Modify existing playbooks
   - Add new inventory groups
   - Create complex multi-tier applications
## 🔧 Troubleshooting

### Common Issues and Solutions

#### 1. VM Won't Start
```bash
# Check VirtualBox status
VBoxManage list runningvms

# Restart VirtualBox service (Windows)
net stop vboxdrv && net start vboxdrv

# Clear Vagrant cache
vagrant destroy
rm -rf .vagrant/
vagrant up
```

#### 2. SSH Connection Failures
```bash
# Inside controller VM, regenerate SSH keys
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""

# Copy public key to nodes manually
for node in 192.168.56.3 192.168.56.4 192.168.56.5; do
  ssh-copy-id -i ~/.ssh/id_rsa.pub vagrant@$node
done
```

#### 3. Ansible Playbook Failures
```bash
# Test connectivity first
ansible all -i ansible/inventory/hosts -m ping

# Run with verbose output
ansible-playbook -i ansible/inventory/hosts ansible/playbooks/first_playbook.yml -v

# Check syntax
ansible-playbook --syntax-check ansible/playbooks/first_playbook.yml
```

#### 4. Shared Folder Issues
```bash
# Reload VM with folder sync
vagrant reload ansible-controller

# Manual mount (if auto-mount fails)
vagrant ssh ansible-controller
sudo mount -t vboxsf vagrant /vagrant
```

### Performance Tips
- **RAM**: Allocate at least 4GB to host machine
- **CPU**: Enable VT-x/AMD-V in BIOS
- **Storage**: Use SSD for better VM performance
- **Network**: Disable Windows Defender real-time scanning for project folder

## 🎓 What You'll Learn

### Ansible Fundamentals
- ✅ Writing and executing playbooks
- ✅ Managing inventory and host groups  
- ✅ Using variables and facts
- ✅ Handling errors and conditionals
- ✅ Working with modules and tasks

### Real-World Applications  
- ✅ Web server configuration (Nginx)
- ✅ Database setup and management (MariaDB)
- ✅ Application deployment (Node.js)
- ✅ System administration tasks
- ✅ Network connectivity testing

### DevOps Best Practices
- ✅ Infrastructure as Code (IaC)
- ✅ Idempotent operations
- ✅ Configuration management
- ✅ Automated deployments
- ✅ Error handling and logging

## 🤝 Contributing

Feel free to:
- Add new playbooks and examples
- Improve existing documentation
- Report issues or bugs
- Suggest enhancements

## 📄 License

This project is for educational purposes. Use freely for learning Ansible!

---

## 🚀 Quick Commands Reference

```bash
# Start learning environment
vagrant up && vagrant ssh ansible-controller

# Navigate to project
cd /vagrant

# Test connectivity  
ansible all -i ansible/inventory/hosts -m ping

# Run basic playbook
ansible-playbook -i ansible/inventory/hosts ansible/playbooks/first_playbook.yml

# Deploy three-tier app
ansible-playbook -i ansible/three-tier-app/hosts.yml ansible/three-tier-app/three-tier-app.yml

# Test internet connectivity
ansible-playbook -i ansible/test-connection/hosts.yml ansible/test-connection/test-connection.yml
```

**Happy Learning! 🎉**
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
sudo apt update
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