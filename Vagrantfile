# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Konfigurasi umum untuk semua VM
  config.vm.box = "debian/bullseye64"
  config.vm.box_version = "11.20241217.1"
  
  # Ansible Controller
  config.vm.define "ansible-controller" do |controller|
    controller.vm.hostname = "ansible-controller"
    
    # Network configuration
    # NAT network for internet access (explicitly configured)
    controller.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", auto_correct: true
    
    # Host-only network for inter-VM communication
    controller.vm.network "private_network", ip: "192.168.56.2"
    
    controller.vm.provider "virtualbox" do |vb|
      vb.name = "ansible-controller"
      vb.memory = "512"
      vb.cpus = 1
      
      # Ensure NAT adapter is enabled for internet access
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end
    
    # Provisioning: Install Ansible dan dependencies
    controller.vm.provision "shell", inline: <<-SHELL
      # Fix DNS resolution first
      echo "Configuring DNS..."
      echo "nameserver 8.8.8.8" > /etc/resolv.conf
      echo "nameserver 8.8.4.4" >> /etc/resolv.conf
      echo "nameserver 1.1.1.1" >> /etc/resolv.conf
      
      # Test DNS resolution
      echo "Testing DNS resolution..."
      nslookup deb.debian.org || echo "DNS test failed, but continuing..."
      
      # Update system
      apt-get update
      
      # Install dependencies
      apt-get install -y software-properties-common
      
      # Install Ansible
      apt-add-repository --yes --update ppa:ansible/ansible
      apt-get install -y ansible
      
      # Install additional tools
      apt-get install -y sshpass python3-pip vim git
      
      # Konfigurasi SSH untuk vagrant user
      mkdir -p /home/vagrant/.ssh
      chmod 700 /home/vagrant/.ssh
      
      # Generate SSH key untuk ansible controller
      if [ ! -f /home/vagrant/.ssh/id_rsa ]; then
        sudo -u vagrant ssh-keygen -t rsa -b 4096 -f /home/vagrant/.ssh/id_rsa -N ""
      fi
      
      # Konfigurasi SSH client
      cat > /home/vagrant/.ssh/config <<EOF
Host 192.168.56.*
    StrictHostKeyChecking no
    UserKnownHostsFile=/dev/null
EOF
      chown vagrant:vagrant /home/vagrant/.ssh/config
      chmod 600 /home/vagrant/.ssh/config
      
      # Create ansible directory structure
      mkdir -p /home/vagrant/ansible/{inventory,playbooks,roles}
      chown -R vagrant:vagrant /home/vagrant/ansible
      
      echo "Ansible Controller setup completed!"
      ansible --version
    SHELL
  end
  
  # Ansible Nodes (3 nodes)
  (1..3).each do |i|
    config.vm.define "ansible-node#{i}" do |node|
      node.vm.hostname = "ansible-node#{i}"
      
      # Network configuration  
      # NAT network for internet access (explicitly ensure it's available)
      node.vm.network "forwarded_port", guest: 22, host: "222#{i}", id: "ssh", auto_correct: true
      
      # Host-only network for inter-VM communication
      node.vm.network "private_network", ip: "192.168.56.#{i+2}"
      
      node.vm.provider "virtualbox" do |vb|
        vb.name = "ansible-node#{i}"
        vb.memory = "512"
        vb.cpus = 1
        
        # Ensure NAT adapter is enabled for internet access
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
      end
      
      # Provisioning: Setup basic dependencies
      node.vm.provision "shell", inline: <<-SHELL
        # Fix DNS resolution first
        echo "Configuring DNS..."
        echo "nameserver 8.8.8.8" > /etc/resolv.conf
        echo "nameserver 8.8.4.4" >> /etc/resolv.conf
        echo "nameserver 1.1.1.1" >> /etc/resolv.conf
        
        # Test DNS resolution
        echo "Testing DNS resolution..."
        nslookup deb.debian.org || echo "DNS test failed, but continuing..."
        
        # Update system
        apt-get update
        
        # Install Python (required for Ansible)
        apt-get install -y python3 python3-pip
        
        # Ensure SSH is running
        systemctl enable ssh
        systemctl start ssh
        
        echo "Node #{i} setup completed!"
      SHELL
    end
  end
end