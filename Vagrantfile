# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.define "ras" do |ras|
    ras.vm.hostname = "ras.loc"
    ras.vm.network "private_network", ip: "192.168.19.30"
    ras.vm.provision "shell", run: "always", inline: <<-SHELL
      sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
      SHELL
  end
  config.vm.define "server" do |server|
    server.vm.hostname = "server.loc"
    server.vm.network "private_network", ip: "192.168.19.10"
    server.vm.provision "shell", run: "always", inline: <<-SHELL
      sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
      SHELL
  end
  config.vm.define "client" do |client|
    client.vm.hostname = "client.loc"
    client.vm.network "private_network", ip: "192.168.19.20"
    client.vm.provision "shell", run: "always", inline: <<-SHELL
      sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
      SHELL
  end
  config.vm.define "provision" do |p|
    p.vm.hostname = "provision.loc"
    p.vm.network "private_network", ip: "192.168.19.2"
    p.vm.provision "shell", run: "always", inline: <<-SHELL
      sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
      yum install epel-release -y
      yum update -y      
      yum install ansible -y
      cp -r /vagrant/* /root/
      cd /root
      ansible-playbook ovpn.yml --limit ovpn1
      ansible-playbook ovpn.yml --limit ovpn2
      SHELL
  end
end
