# -*- mode: ruby -*-
# vi: set ft=ruby :

server_ips            = ["192.168.22.12", "192.168.22.13"]
box_names             = ["server1", "server2"] #also the name of the directory to sync folders
host_names            = ["server1.dev", "server2.dev"]
hosts                 = [8000, 8001]
box_distro            = "ubuntu/trusty64"
server_cpus           = "1"   # Cores
server_memory         = "1024" # MB
server_swap           = "2048" # Options: false | int (MB) - Guideline: Between one or two times the server_memory
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  # config.vm.box = "base"
  # ------------------------
  # Set up multiple machines
  
  config.ssh.forward_agent = true

  server_ips.zip(box_names, host_names, hosts).each do |server_ip, box_name, host_name, a_host|
    config.vm.define box_name + "box" do |abox|
      abox.vm.hostname = host_name
      abox.vm.box = box_distro
      abox.vm.network :private_network, ip: server_ip
      abox.vm.network :forwarded_port, guest: 80, host: a_host
      abox.vm.synced_folder box_name, "/vagrant",
              id: "core",
              :nfs => true,
              :mount_options => ['nolock,vers=3,udp,noatime']
      abox.vm.provider :virtualbox do |vb|
        vb.name = host_name
        # Set server cpus
        vb.customize ["modifyvm", :id, "--cpus", server_cpus]

        # Set server memory
        vb.customize ["modifyvm", :id, "--memory", server_memory]

        # Set the timesync threshold to 10 seconds, instead of the default 20 minutes.
        # If the clock gets more than 15 minutes out of sync (due to your laptop going
        # to sleep for instance, then some 3rd party services will reject requests.
        vb.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]

        # Prevent VMs running on Ubuntu to lose internet connection
        # vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        # vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
      end
    end
  end
  # ------------------------
  

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    echo "Installing Apache.."
    sudo apt-get install -y apache2
    echo "Installing Tomcat.."
    sudo apt-get install -y tomcat7
    echo "Installing Tomcat7 docs.."
    sudo apt-get install -y tomcat7-docs
    echo "Installing Tomcat7 administration webapps.."
    sudo apt-get install -y tomcat7-admin
    echo "Installing Tomcat7 examples webapps.."
    sudo apt-get install tomcat7-examples
    echo "Installing Git.."
    sudo apt-get install -y git
    echo "Installing Maven.."
    sudo apt-get install -y maven
    echo "Installing Java 8.."
    sudo apt-get install -y software-properties-common python-software-properties
    echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | sudo /usr/bin/debconf-set-selections
    sudo add-apt-repository ppa:webupd8team/java -y
    sudo apt-get update
    sudo apt-get install --reinstall ca-certificates
    sudo apt-get install oracle-java8-installer
    echo "Setting environment variables for Java 8.."
    sudo apt-get install -y oracle-java8-set-default
  SHELL
end
