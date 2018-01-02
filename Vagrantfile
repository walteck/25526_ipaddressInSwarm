# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile to spin up two VM's running Docker

unless Vagrant.has_plugin?("vagrant-docker-compose")
  system("vagrant plugin install vagrant-docker-compose")
  puts "Dependencies installed, please try the command again."
  exit
end

Vagrant.configure("2") do |config|
  config.vm.define "manager" do |manager|
    manager.vm.box = "ubuntu/trusty64"
	manager.vm.box_check_update = true
	manager.vm.hostname = "manager"

	manager.vm.synced_folder ".", "/vagrant"

	manager.vm.provider "virtualbox" do |vb|
		# Display the VirtualBox GUI when booting the machine
		vb.gui = true
		# Switch off 3D Acceleration on the gues as it is very host-dependent
		vb.customize ["modifyvm", :id, "--accelerate3d", "off"]
		# Customize the amount of memory on the VM:
		vb.customize ["modifyvm", :id, "--memory", 512]
		vb.customize ["modifyvm", :id, "--name", "manager"]
		vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
	end

  # install docker
	manager.vm.provision :docker
	manager.vm.provision "shell", inline: "curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose", privileged: true
	manager.vm.provision "shell", inline: "chmod +x /usr/local/bin/docker-compose", privileged: true
	
    manager.vm.network :private_network, ip: "192.168.56.120"
	
      if File.file?("./hosts") 
        manager.vm.provision "file", source: "hosts", destination: "/tmp/hosts"
        manager.vm.provision "shell", inline: "cat /tmp/hosts >> /etc/hosts", privileged: true
      end 
	  
	  manager.vm.provision "shell", inline: "docker swarm init --advertise-addr 192.168.56.120"
	  manager.vm.provision "shell", inline: "docker swarm join-token -q worker > /vagrant/token"
	  
	  #Build our containers
	  manager.vm.provision "file", source: "containers", destination: "$HOME/containers"
	  #Deploy our stack
	  manager.vm.provision "shell", inline: "docker service create --name registry --publish published=5000,target=5000 registry:2"
	  manager.vm.provision "shell", inline: "pushd /home/vagrant/containers && docker-compose up -d && popd "
	  manager.vm.provision "shell", inline: "pushd /home/vagrant/containers && docker-compose down && popd "
	  manager.vm.provision "shell", inline: "pushd /home/vagrant/containers && docker-compose push && popd "
	  manager.vm.provision "shell", inline: "docker stack deploy --compose-file /home/vagrant/containers/docker-compose.yml stackdemo"
  end
  config.vm.define "node01" do |node01|
    node01.vm.box = "ubuntu/trusty64"
	node01.vm.box_check_update = true
	node01.vm.hostname = "node01"

	node01.vm.synced_folder ".", "/vagrant"

	node01.vm.provider "virtualbox" do |vb|
		# Display the VirtualBox GUI when booting the machine
		vb.gui = true
		# Switch off 3D Acceleration on the gues as it is very host-dependent
		vb.customize ["modifyvm", :id, "--accelerate3d", "off"]
		# Customize the amount of memory on the VM:
		vb.customize ["modifyvm", :id, "--memory", 512]
		vb.customize ["modifyvm", :id, "--name", "node01"]
		vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
	end

  # install docker
	node01.vm.provision :docker
	
    node01.vm.network :private_network, ip: "192.168.56.121"
      if File.file?("./hosts") 
        node01.vm.provision "file", source: "hosts", destination: "/tmp/hosts"
        node01.vm.provision "shell", inline: "cat /tmp/hosts >> /etc/hosts", privileged: true
      end 
	  
	node01.vm.provision "shell", inline: "docker swarm join --advertise-addr 192.168.56.121 --listen-addr 192.168.56.121:2377 --token `cat /vagrant/token` 192.168.56.120:2377"
  end
  config.vm.define "node02" do |node02|
    node02.vm.box = "ubuntu/trusty64"
	node02.vm.box_check_update = true
	node02.vm.hostname = "node02"

	node02.vm.synced_folder ".", "/vagrant"

	node02.vm.provider "virtualbox" do |vb|
		# Display the VirtualBox GUI when booting the machine
		vb.gui = true
		# Switch off 3D Acceleration on the gues as it is very host-dependent
		vb.customize ["modifyvm", :id, "--accelerate3d", "off"]
		# Customize the amount of memory on the VM:
		vb.customize ["modifyvm", :id, "--memory", 512]
		vb.customize ["modifyvm", :id, "--name", "node02"]
		vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
	end

  # install docker
	node02.vm.provision :docker
	
    node02.vm.network :private_network, ip: "192.168.56.122"
      if File.file?("./hosts") 
        node02.vm.provision "file", source: "hosts", destination: "/tmp/hosts"
        node02.vm.provision "shell", inline: "cat /tmp/hosts >> /etc/hosts", privileged: true
      end 

	node02.vm.provision "shell", inline: "docker swarm join --advertise-addr 192.168.56.122 --listen-addr 192.168.56.122:2377 --token `cat /vagrant/token` 192.168.56.120:2377"
  end
  

  config.vm.define "client" do |client|
    client.vm.box = "ubuntu/trusty64"
	client.vm.box_check_update = true
	client.vm.hostname = "client"

	client.vm.synced_folder ".", "/vagrant"

	client.vm.provider "virtualbox" do |vb|
		# Display the VirtualBox GUI when booting the machine
		vb.gui = true
		# Switch off 3D Acceleration on the gues as it is very host-dependent
		vb.customize ["modifyvm", :id, "--accelerate3d", "off"]
		# Customize the amount of memory on the VM:
		vb.customize ["modifyvm", :id, "--memory", 512]
		vb.customize ["modifyvm", :id, "--name", "client"]
		vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
	end
      if File.file?("./hosts") 
        client.vm.provision "file", source: "hosts", destination: "/tmp/hosts"
        client.vm.provision "shell", inline: "cat /tmp/hosts >> /etc/hosts", privileged: true
      end 
    client.vm.network :private_network, ip: "192.168.56.150"

  end
end
